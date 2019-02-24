# spring和hibernate的整合过程

* 在整合过程中遇到的坑有：
* spring和hibernate的大版本不一致导致了无法加载sessionFactory
* 在application.xml中配置事务管理器transactionManager中没有准确地把sessionFactory属性传入该构造器参数中
* hibernate中写的HQL语句必须要和类名保持一致



一个例子：

application.xml

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">



    <context:component-scan base-package="com.spring_hiber"></context:component-scan>
    <context:property-placeholder location="classpath:db.properties"></context:property-placeholder>

    <!--配置C3p0-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${jdbc.user}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="driverClass" value="${jdbc.driverClass}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="initialPoolSize" value="${jdbc.initPoolSize}"></property>
        <property name="maxPoolSize" value="${jdbc.maxPoolSize}"></property>
    </bean>


    <!--配置Hibernate的SessionFactory实例：通过spring提供的LocalSessionFactoryBean进行配置-->
    <bean id="sessionFactory" class="org.springframework.orm.hibernate4.LocalSessionFactoryBean">
        <!--配置数据源属性-->
        <property name="dataSource" ref="dataSource"></property>
        
        
        
        <!--配置hibernate配置文件的位置和名称-->
        <property name="configLocation" value="classpath:hibernate.cfg.xml"></property>
        <!-- 使用 hibernateProperties 属相来配置 Hibernate 原生的属性来替代上面的configLocation -->
		<property name="hibernateProperties">
			<props>
				<prop key="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</prop>
				<prop key="hibernate.show_sql">true</prop>
				<prop key="hibernate.format_sql">true</prop>
				<prop key="hibernate.hbm2ddl.auto">update</prop>
			</props>
		</property>
        
        
        
        
        <!--&lt;!&ndash;配置hibernate映射文件的位置和名称，可以使用通配符&ndash;&gt;-->
        //这个方法会出现重复读取xml的异常，注释掉就好，原因未明
        <!--<property name="mappingLocations" value="classpath:com/spring_hiber/entities/*.hbm.xml"></property>-->
    </bean>

    <!--配置spring的声明式事务-->
    <!--1、配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.orm.hibernate4.HibernateTransactionManager">
        <property name="sessionFactory" ref="sessionFactory"></property>
    </bean>

    <!--2、配置事务属性，需要事务管理器-->
    <tx:advice id="txadvice" transaction-manager="transactionManager">
        <tx:attributes>
            <tx:method name="get*" read-only="true"/>
            <tx:method name="*"/>
            //这个是给事务传递方式的改变，表明这个方法每一次调用都会单独创建新的事务
            <tx:method name="purchase" propagation="REQUIRES_NEW"></tx:method>
        </tx:attributes>
    </tx:advice>

    <!--3、配置事务切点，并且将切点和事务属性关联起来-->
    <aop:config>
        <aop:pointcut id="txPointcut" expression="execution(* com.spring_hiber.service.*.*(..))"></aop:pointcut>
        <aop:advisor advice-ref="txadvice" pointcut-ref="txPointcut"></aop:advisor>
    </aop:config>

</beans>
```

hibernate.cfg.xml

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
    "-//Hibernate/Hibernate Configuration DTD//EN"
    "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
  <session-factory>

    <!-- 配置 hibernate 的基本属性 -->
    <!-- 1. 数据源需配置到 IOC 容器中, 所以在此处不再需要配置数据源 -->
    <!-- 2. 关联的 .hbm.xml 也在 IOC 容器配置 SessionFactory 实例时在进行配置 -->
    <!-- 3. 配置 hibernate 的基本属性: 方言, SQL 显示及格式化, 生成数据表的策略以及二级缓存等. -->
    <property name="hibernate.dialect">org.hibernate.dialect.MySQL5InnoDBDialect</property>
    <property name="hibernate.show_sql">true</property>
    <property name="hibernate.format_sql">true</property>
    <property name="hibernate.hbm2ddl.auto">update</property>
    <property name="connection.url">jdbc:mysql://localhost:3306/spring</property>
    <property name="connection.driver_class">com.mysql.jdbc.Driver</property>
    <mapping class="com.spring_hiber.entities.AccountEntity"/>
    <mapping resource="com/spring_hiber/entities/AccountEntity.hbm.xml"/>
    <mapping resource="com/spring_hiber/entities/BookEntity.hbm.xml"/>
    <mapping class="com.spring_hiber.entities.BookEntity"/>


    <!-- <property name="connection.username"/> -->
    <!-- <property name="connection.password"/> -->

    <!-- DB schema will be updated if needed -->
    <!-- <property name="hbm2ddl.auto">update</property> -->
  </session-factory>
</hibernate-configuration>

```

db.properties

```
jdbc.user=root
jdbc.password=085436
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.jdbcUrl=jdbc:mysql:///spring?useSSL=false


jdbc.initPoolSize=5
jdbc.maxPoolSize=10
```

几个实现类（接口就不写了）

```
BookShopDaoImpl
@Repository
public class BookShopDaoImpl implements BookShopDao {
    @Autowired
    private SessionFactory sessionFactory;

    private Session getSession() {
        return sessionFactory.getCurrentSession();
    }

    @Override
    public int findBookPriceByIsbn(String isbn) {
    //这里写的HQL语句一定要注意和实体类名字保持一致，区分大小写
        String hql = "select b.price from BookEntity b where b.isbn=?";
//        不再使用setString等方法，直接使用setParameter
        Query query = getSession().createQuery(hql).setParameter(0, isbn);
        return (Integer) query.uniqueResult();
    }

    @Override
    public void updateBookStock(String isbn) {
        String hql2 = "SELECT b.stock FROM BookEntity b WHERE b.isbn = ?";
        int stock = (int) getSession().createQuery(hql2).setParameter(0, isbn).uniqueResult();
        if (stock == 0) {
            throw new BookStockException("库存不足!");
        }

        String hql = "UPDATE BookEntity b SET b.stock = b.stock - 1 WHERE b.isbn = ?";
        getSession().createQuery(hql).setParameter(0, isbn).executeUpdate();
    }

    @Override
    public void updateUserAccount(String username, int price) {
        //验证余额是否足够
        String hql2 = "SELECT a.balance FROM AccountEntity a WHERE a.username = ?";
        int balance = (int) getSession().createQuery(hql2).setParameter(0, username).uniqueResult();
        if (balance < price) {
            throw new BookStockException("余额不足!");
        }

        String hql = "UPDATE AccountEntity a SET a.balance = a.balance - ? WHERE a.username = ?";
        getSession().createQuery(hql).setParameter(0, price).setParameter(1, username).executeUpdate();
    }
}







@Service
public class BookShopServiceImpl implements BookShopService {

    @Autowired
    private BookShopDao bookShopDao;

    @Override
    public void purchase(String username, String isbn) {
        int price = bookShopDao.findBookPriceByIsbn(isbn);
        bookShopDao.updateBookStock(isbn);
        bookShopDao.updateUserAccount(username, price);
    }
}






@Repository
public class CashierImpl implements Cashier {
    @Autowired
    private BookShopService bookShopService;

    @Override
    public void checkout(String username, List<String> isbns) {
        for (String isbn :
                isbns) {
            bookShopService.purchase(username, isbn);
        }
    }
}


```



