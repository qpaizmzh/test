# Hibernate的简单介绍和简单的对象映射方法

![](/assets/hiber-1.png)![](/assets/hiber-2.png)

![](/assets/hiber-3.png)

for instance（idea）:

* 创建需要映射的表，便按照教程将sql进行连接：
* 创建hibernate的项目，并将hibernate中的hibernate.cfg.xml中相关信息配置好

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!--这里要在URL中添加?serverTimezone=UTC才能正常运行，原因是数据库连接做了相关验证-->
        <property name="connection.url">jdbc:mysql://localhost:3306/hiber?serverTimezone=UTC</property>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <!--不太了解选的不同方言会有什么效果-->
        <property name="dialect">org.hibernate.dialect.MySQL57Dialect</property>


        <property name="connection.username">root</property>
        <property name="connection.password">085436</property>
        <!--格式化sql语句，同时在命令太显示sql语句，方便阅读-->
        <property name="format_sql">true</property>
        <property name="show_sql">true</property>

        <!--用来映射数据库表和实体类关系的属性标签，格式像如下，是在映像的时候自动生成的-->
        <mapping class="com.hibernate.hello.ArtiEntity"/>
        <mapping resource="com/hibernate/hello/ArtiEntity.hbm.xml"/>
        <!-- <property name="connection.username"/> -->
        <!-- <property name="connection.password"/> -->

        <!-- DB schema will be updated if needed -->
        <!-- <property name="hbm2ddl.auto">update</property> -->
    </session-factory>
</hibernate-configuration>
```

* 映像完成后会生成对应的实体类和反应关系的XML文件，在实体类的文件中添加带参和不带参的构造器
* 按照下面的流程创建或者修改对应的数据库的操作：

```
 //1、完成对sessionFactory的创建
        SessionFactory sessionFactory = null;
        //Configuration类在PPT有介绍它的功能
        Configuration configuration = new Configuration().configure();
        //新版的原因，现在加载configuration需要添加实体类的class属性
        configuration.addClass(ArtiEntity.class);
        //服务上的注册，需要前面的configuration的配置完成
        ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder().applySettings(configuration.getProperties()).build();
        sessionFactory = configuration.buildSessionFactory(serviceRegistry);

        //2、创建对应的session
        Session session = sessionFactory.openSession();

        //3、事务
        Transaction transaction = session.getTransaction();
        transaction.begin();

        //4、创建要保存的对象
        ArtiEntity artiEntity = new ArtiEntity("fuck", "jesus", new Date(new java.util.Date().getTime()));
        session.save(artiEntity);

        //提交事务
        transaction.commit();

        //关闭
        session.close();
        sessionFactory.close();
    
```



