# 关联关系（oneToMany和ManyToOne）

* 一共是两个实体类：Customer和Order
* Customer的属性是：

```
    private Integer customerId;
    private String customerName;

    //    在集合里面提前设置实例，可以防止有效防止代码出现空指针异常
    private Set<Order> orderSet = new HashSet<>();
```

* Customer的映射文件是：

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    //table写的是数据库的表名
    <class name="com.hibernate.hello.Customer" table="customer">
        //这里的name属性指的是实体类的某个属性，column是数据库的字段名
        <id name="customerId" column="idcustomer">
        //这里是设置id生成的策略
            <generator class="native"></generator>
        </id>
        <property name="customerName" column="customer_name"/>
        //在设置一对多的映射关系的时候，要先把Customer的属性名字和要映射的数据表对应上
        <set name="orderSet" table="orders">
            //这里写的是customer实体类对应的表的主键
            <key column="customerid"></key>
            //这里填写的属性是要映射另一方关系的实体类
            <one-to-many class="com.hibernate.hello.Order"></one-to-many>
        </set>
    </class>
</hibernate-mapping>
```

* Order的属性是：

```
    private Integer orderId;
    private String orderName;
    private Customer customer;
```

* Order的映射文件：

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
<!-- 映射 1 对多的那个集合属性 -->
        <!-- set: 映射 set 类型的属性, table: set 中的元素对应的记录放在哪一个数据表中. 该值需要和多对一的多的那个表的名字一致 -->
        <!-- inverse: 指定由哪一方来维护关联关系. 通常设置为 true, 以指定由多的一端来维护关联关系 -->
        <!-- cascade 设定级联操作. 开发时不建议设定该属性. 建议使用手工的方式来处理 -->
        <!-- order-by 在查询时对集合中的元素进行排序, order-by 中使用的是表的字段名, 而不是持久化类的属性名  -->
    <class name="com.hibernate.hello.Order" table="orders">
    //这里的name属性指的是实体类的某个属性，column是数据库的字段名
        <id name="orderId" column="idorder">
        //这里是设置id生成的策略
            <generator class="native"></generator>
        </id>
        <property name="orderName" column="ordername"/>
        //这里映射的多对一关系的时候，name属性对应的是Order类的属性，column就是对应的是要映射的数据表的字段名
        <many-to-one name="customer" column="customerid"></many-to-one>
    </class>
</hibernate-mapping>
```

* hibernate.cfg.xml文件的配置信息

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!--这里要在URL中添加?serverTimezone=UTC才能正常运行，原因是数据库连接做了相关验证useUnicode=true&amp;characterEncoding=utf-8&amp;useSSL=false-->
        <property name="connection.url">jdbc:mysql://localhost:3306/hiber?serverTimezone=UTC</property>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <property name="connection.username">root</property>
        <property name="connection.password">085436</property>
        <!--不太了解选的不同方言会有什么效果-->
        <property name="dialect">org.hibernate.dialect.MySQL57Dialect</property>


        <!--格式化sql语句，同时在命令太显示sql语句，方便阅读-->
        <property name="format_sql">true</property>
        <property name="show_sql">true</property>
        <property name="hbm2ddl.auto">update</property>
        <property name="current_session_context_class">thread</property>
        <property name="connection.autocommit">true</property>

        <!--&lt;!&ndash;配置C3P0&ndash;&gt;-->
        <!--<property name="hibernate.c3p0.max_size">10</property>-->
        <!--<property name="hibernate.c3p0.min_size">2</property>-->
        <!--<property name="hibernate.c3p0.acquire_increment">2</property>-->
        <!--<property name="hibernate.c3p0.idle_test_period">2000</property>-->
        <!--<property name="hibernate.c3p0.max_statements">10</property>-->
        <!--<property name="hibernate.c3p0.timeout">2000</property>-->

        <!--用来映射数据库表和实体类关系的属性标签，格式像如下-->
        <mapping class="com.hibernate.hello.ArtiEntity"/>
        <mapping resource="com/hibernate/hello/ArtiEntity.hbm.xml"/>
        <mapping class="com.hibernate.hello.Customer"></mapping>
        <mapping resource="com/hibernate/hello/Customer.hbm.xml"></mapping>
        <mapping class="com.hibernate.hello.Order"></mapping>
        <mapping resource="com/hibernate/hello/Order.hbm.xml"></mapping>

    </session-factory>
</hibernate-configuration>
```

* 测试方法的类：

```
public class testTest {
    private Session session;
    private Transaction transaction;
    SessionFactory sessionFactory;

    @Before
    public void before() {
        //1、完成对sessionFactory的创建
        Configuration configuration = new Configuration().configure();
        //新版hibernate需要在配置文件添加Class（不知道为何）
        configuration.addClass(ArtiEntity.class);
        configuration.addClass(Customer.class);
        configuration.addClass(Order.class);


        ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder().applySettings(configuration.getProperties()).build();
        sessionFactory = configuration.buildSessionFactory(serviceRegistry);

        //2、创建对应的session
        session = sessionFactory.openSession();

        //3、事务
//        transaction = session.getTransaction();
//        transaction.begin();
        transaction = session.beginTransaction();
    }

     //插入的例子
    @Test
    public void testmany2one() {
        Customer customer = new Customer();
        customer.setCustomerName("A");
        Serializable cusid = session.save(customer);
        customer.setCustomerId(Integer.valueOf(cusid.toString()));

        Order order = new Order();
        Order order2 = new Order();
        order.setOrderName("BB");
        order2.setOrderName("BBB");
        order.setCustomer(customer);
        order2.setCustomer(customer);
            //执行  save 操作: 先插入 Customer, 再插入 Order, 3 条 INSERT, 2 条 UPDATE
        //因为 1 的一端和 n 的一端都维护关联关系. 所以会多出 UPDATE
        //可以在 1 的一端的 set 节点指定 inverse=true, 来使 1 的一端放弃维护关联关系!
        //建议设定 set 的 inverse=true, 建议先插入 1 的一端, 后插入多的一端
        //好处是不会多出 UPDATE 语句
        Serializable id = session.save(order);
        Serializable id2 = session.save(order2);
        order.setOrderId(Integer.valueOf(id.toString()));
        order2.setOrderId(Integer.valueOf(id2.toString()));
    }

    @Test
    public void testDelete(){
        //在不设定级联关系的情况下, 且 1 这一端的对象有 n 的对象在引用, 不能直接删除 1 这一端的对象
        Customer customer = (Customer) session.get(Customer.class, 1);
        session.delete(customer); 
    }

    @Test
    public void testUpdat2(){
        Customer customer = (Customer) session.get(Customer.class, 1);
        customer.getOrders().iterator().next().setOrderName("GGG"); 
    }

    @Test
    public void testUpdate(){
        Order order = (Order) session.get(Order.class, 1);
        order.getCustomer().setCustomerName("AAA");
    }

    @Test
    public void testOne2ManyGet(){
        //1. 对 n 的一端的集合使用延迟加载
        Customer customer = (Customer) session.get(Customer.class, 7);
        System.out.println(customer.getCustomerName()); 
        //2. 返回的多的一端的集合时 Hibernate 内置的集合类型. 
        //该类型具有延迟加载和存放代理对象的功能. 
        System.out.println(customer.getOrders().getClass()); 

        //session.close();
        //3. 可能会抛出 LazyInitializationException 异常 

        System.out.println(customer.getOrders().size()); 

        //4. 再需要使用集合中元素的时候进行初始化. 
    }

    @Test
    public void testMany2OneGet(){
        //1. 若查询多的一端的一个对象, 则默认情况下, 只查询了多的一端的对象. 而没有查询关联的
        //1 的那一端的对象!
        Order order = (Order) session.get(Order.class, 1);
        System.out.println(order.getOrderName()); 

        System.out.println(order.getCustomer().getClass().getName());

        session.close();

        //2. 在需要使用到关联的对象时, 才发送对应的 SQL 语句. 
        Customer customer = order.getCustomer();
        System.out.println(customer.getCustomerName()); 

        //3. 在查询 Customer 对象时, 由多的一端导航到 1 的一端时, 
        //若此时 session 已被关闭, 则默认情况下
        //会发生 LazyInitializationException 异常

        //4. 获取 Order 对象时, 默认情况下, 其关联的 Customer 对象是一个代理对象!

    }



    @After
    public void after() {
        //提交事务
        transaction.commit();

        //关闭
        session.close();
        sessionFactory.close();
    }
}
```



