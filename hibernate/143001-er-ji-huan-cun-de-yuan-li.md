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
    	
    	//除了可以在hibernate的配置文件配置二级缓存以外，还可以针对实体类的XML文件使用标签进行配置
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



