# 二级缓存的原理

![](/assets/hiber-14-1.png)![](/assets/hibernate-14-2.png)![](/assets/hibernate-14-3.png)

方法步骤：

```
1. 使用 Hibernate 二级缓存的步骤:

1). 加入二级缓存插件的 jar 包及配置文件:

I. 复制 \hibernate-release-4.2.4.Final\lib\optional\ehcache\*.jar 到当前 Hibrenate 应用的类路径下.
II. 复制 hibernate-release-4.2.4.Final\project\etc\ehcachexml 到当前 WEB 应用的类路径下

2). 配置 hibernate.cfg.xml 

I.   配置启用 hibernate 的二级缓存
<property name="cache.use_second_level_cache">true</property>

II.  配置hibernate二级缓存使用的产品
<property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>

III. 配置对哪些类使用 hibernate 的二级缓存
<class-cache usage="read-write" class="com.atguigu.hibernate.entities.Employee"/>

实际上也可以在 .hbm.xml 文件中配置对哪些类使用二级缓存, 及二级缓存的策略是什么.
```

代码例子：

```
@Test
    public void testHibernateSecondLevelCache(){
        Employee employee = (Employee) session.get(Employee.class, 100);
        System.out.println(employee.getName()); 

        transaction.commit();
        session.close();

        session = sessionFactory.openSession();
        transaction = session.beginTransaction();

        Employee employee2 = (Employee) session.get(Employee.class, 100);
        System.out.println(employee2.getName()); 
    }
```

hibernate.fg.xml:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>

        <!-- Hibernate 连接数据库的基本信息 -->
        <property name="connection.username">scott</property>
        <property name="connection.password">java</property>
        <property name="connection.driver_class">oracle.jdbc.driver.OracleDriver</property>
        <property name="connection.url">jdbc:oracle:thin:@localhost:1521:orcl</property>

        <!-- Hibernate 的基本配置 -->
        <!-- Hibernate 使用的数据库方言 -->
        <property name="dialect">org.hibernate.dialect.Oracle10gDialect</property>

        <!-- 运行时是否打印 SQL -->
        <property name="show_sql">true</property>

        <!-- 运行时是否格式化 SQL -->
        <property name="format_sql">true</property>

        <!-- 生成数据表的策略 -->
        <property name="hbm2ddl.auto">update</property>

        <!-- 设置 Hibernate 的事务隔离级别 -->
        <property name="connection.isolation">2</property>

        <!-- 删除对象后, 使其 OID 置为 null -->
        <property name="use_identifier_rollback">true</property>

        <!-- 配置 C3P0 数据源 -->
        <!--  
        <property name="hibernate.c3p0.max_size">10</property>
        <property name="hibernate.c3p0.min_size">5</property>
        <property name="c3p0.acquire_increment">2</property>

        <property name="c3p0.idle_test_period">2000</property>
        <property name="c3p0.timeout">2000</property>

        <property name="c3p0.max_statements">10</property>
        -->

        <!-- 设定 JDBC 的 Statement 读取数据的时候每次从数据库中取出的记录条数 -->
        <property name="hibernate.jdbc.fetch_size">100</property>

        <!-- 设定对数据库进行批量删除，批量更新和批量插入的时候的批次大小 -->
        <property name="jdbc.batch_size">30</property>

        <!-- 启用二级缓存 -->
        <property name="cache.use_second_level_cache">true</property>

        <!-- 配置使用的二级缓存的产品 -->
        <property name="hibernate.cache.region.factory_class">org.hibernate.cache.ehcache.EhCacheRegionFactory</property>

        <!-- 配置启用查询缓存 -->
        <property name="cache.use_query_cache">true</property>

        <!-- 配置管理 Session 的方式 -->
        <property name="current_session_context_class">thread</property>

        <!-- 需要关联的 hibernate 映射文件 .hbm.xml -->
        <mapping resource="com/atguigu/hibernate/entities/Department.hbm.xml"/>
        <mapping resource="com/atguigu/hibernate/entities/Employee.hbm.xml"/>
        //这里才是设置缓存的类的标签
        <class-cache usage="read-write" class="com.atguigu.hibernate.entities.Employee"/>
        <class-cache usage="read-write" class="com.atguigu.hibernate.entities.Department"/>
        <collection-cache usage="read-write" collection="com.atguigu.hibernate.entities.Department.emps"/>
    </session-factory>
</hibernate-configuration>
```

同样的也可以在对应的实体类对应的XML文件进行配置：

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>

    <class name="com.atguigu.hibernate.entities.Employee" table="GG_EMPLOYEE">

        //除了可以在hibernate的配置文件配置二级缓存以外，还可以针对实体类的XML文件使用标签进行配置，不需要在hibernate的配置
        //文件配置
        <cache usage="read-write"/>


        <id name="id" type="java.lang.Integer">
            <column name="ID" />
            <generator class="native" />
        </id>

        <property name="name" type="java.lang.String">
            <column name="NAME" />
        </property>

        <property name="salary" type="float">
            <column name="SALARY" />
        </property>

        <property name="email" type="java.lang.String">
            <column name="EMAIL" />
        </property>

        <many-to-one name="dept" class="com.atguigu.hibernate.entities.Department">
            <column name="DEPT_ID" />
        </many-to-one>

    </class>

    <query name="salaryEmps"><![CDATA[FROM Employee e WHERE e.salary > :minSal AND e.salary < :maxSal]]></query>

</hibernate-mapping>
```

另外附ehcache.xml的配置方法：

```
<ehcache>

    <!-- Sets the path to the directory where cache .data files are created.

         If the path is a Java System Property it is replaced by
         its value in the running VM.

         The following properties are translated:
         user.home - User's home directory
         user.dir - User's current working directory
         java.io.tmpdir - Default temp file path -->
    <!--  
    	指定一个目录：当 EHCache 把数据写到硬盘上时, 将把数据写到这个目录下.
    -->     
    <diskStore path="d:\\tempDirectory"/>


    <!--Default Cache configuration. These will applied to caches programmatically created through
        the CacheManager.

        The following attributes are required for defaultCache:

        maxInMemory       - Sets the maximum number of objects that will be created in memory
        eternal           - Sets whether elements are eternal. If eternal,  timeouts are ignored and the element
                            is never expired.
        timeToIdleSeconds - Sets the time to idle for an element before it expires. Is only used
                            if the element is not eternal. Idle time is now - last accessed time
        timeToLiveSeconds - Sets the time to live for an element before it expires. Is only used
                            if the element is not eternal. TTL is now - creation time
        overflowToDisk    - Sets whether elements can overflow to disk when the in-memory cache
                            has reached the maxInMemory limit.

        -->
    <!--  
    	设置缓存的默认数据过期策略 
    -->    
    <defaultCache
        maxElementsInMemory="10000"
        eternal="false"
        timeToIdleSeconds="120"
        timeToLiveSeconds="120"
        overflowToDisk="true"
        />

   	<!--  
   		设定具体的命名缓存的数据过期策略。每个命名缓存代表一个缓存区域
   		缓存区域(region)：一个具有名称的缓存块，可以给每一个缓存块设置不同的缓存策略。
   		如果没有设置任何的缓存区域，则所有被缓存的对象，都将使用默认的缓存策略。即：<defaultCache.../>
   		Hibernate 在不同的缓存区域保存不同的类/集合。
			对于类而言，区域的名称是类名。如:com.atguigu.domain.Customer
			对于集合而言，区域的名称是类名加属性名。如com.atguigu.domain.Customer.orders
   	-->
   	<!--  
   		name: 设置缓存的名字,它的取值为类的全限定名或类的集合的名字 
		maxElementsInMemory: 设置基于内存的缓存中可存放的对象最大数目 
		
		eternal: 设置对象是否为永久的, true表示永不过期,
		此时将忽略timeToIdleSeconds 和 timeToLiveSeconds属性; 默认值是false 
		timeToIdleSeconds:设置对象空闲最长时间,以秒为单位, 超过这个时间,对象过期。
		当对象过期时,EHCache会把它从缓存中清除。如果此值为0,表示对象可以无限期地处于空闲状态。 
		timeToLiveSeconds:设置对象生存最长时间,超过这个时间,对象过期。
		如果此值为0,表示对象可以无限期地存在于缓存中. 该属性值必须大于或等于 timeToIdleSeconds 属性值 
		
		overflowToDisk:设置基于内存的缓存中的对象数目达到上限后,是否把溢出的对象写到基于硬盘的缓存中 
   	-->
    <cache name="com.atguigu.hibernate.entities.Employee"
        maxElementsInMemory="1"
        eternal="false"
        timeToIdleSeconds="300"
        timeToLiveSeconds="600"
        overflowToDisk="true"
        />

    <cache name="com.atguigu.hibernate.entities.Department.emps"
        maxElementsInMemory="1000"
        eternal="true"
        timeToIdleSeconds="0"
        timeToLiveSeconds="0"
        overflowToDisk="false"
        />

</ehcache>

```



