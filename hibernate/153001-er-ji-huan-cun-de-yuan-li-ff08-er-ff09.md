# 二级缓存的原理（二）

![](/assets/hiber-15-1.png)![](/assets/hiber-15-2.png)这是使用Set集合作二级缓存的设置流程：

```
2). 集合级别的二级缓存的配置

I. 配置对集合使用二级缓存

<collection-cache usage="read-write" collection="com.atguigu.hibernate.entities.Department.emps"/>

也可以在 .hbm.xml 文件中进行配置

<set name="emps" table="GG_EMPLOYEE" inverse="true" lazy="true">
    <cache usage="read-write"/>
    <key>
        <column name="DEPT_ID" />
    </key>
    <one-to-many class="com.atguigu.hibernate.entities.Employee" />
</set>

II. 注意: 还需要配置集合中的元素对应的持久化类也使用二级缓存! 否则将会多出 n 条 SQL 语句. 

3). ehcache 的 配置文件: ehcache.xml

4).  查询缓存: 默认情况下, 设置的缓存对 HQL 及 QBC 查询时无效的, 但可以通过以下方式使其是有效的

I.  在 hibernate 配置文件中声明开启查询缓存

<property name="cache.use_query_cache">true</property>

II. 调用 Query 或 Criteria 的 setCacheable(true) 方法

III. 查询缓存依赖于二级缓存
```

```
    @Test
    public void testCollectionSecondLevelCache(){
        Department dept = (Department) session.get(Department.class, 80);
        System.out.println(dept.getName());
        System.out.println(dept.getEmps().size()); 

        //测试二级缓存用的，可以直接不用管
        transaction.commit();
        session.close();

        session = sessionFactory.openSession();
        transaction = session.beginTransaction();

        Department dept2 = (Department) session.get(Department.class, 80);
        System.out.println(dept2.getName());
        System.out.println(dept2.getEmps().size()); 
    }

    @Test
    public void testHibernateSecondLevelCache(){
        Employee employee = (Employee) session.get(Employee.class, 100);
        System.out.println(employee.getName()); 

        //测试二级缓存用的，可以直接不用管
        transaction.commit();
        session.close();

        session = sessionFactory.openSession();
        transaction = session.beginTransaction();

        Employee employee2 = (Employee) session.get(Employee.class, 100);
        System.out.println(employee2.getName()); 
    }
```

同样的，可以使用Query和criteria来设置二级缓存

![](/assets/hiber-15-3.png)

```
@Test
    public void testQueryCache(){
        //query也可以设置二级缓存
        Query query = session.createQuery("FROM Employee");
        query.setCacheable(true);

        List<Employee> emps = query.list();
        System.out.println(emps.size());

        emps = query.list();
        System.out.println(emps.size());
        //criteria也可以设置
        Criteria criteria = session.createCriteria(Employee.class);
        criteria.setCacheable(true);
    }
```

时间戳缓存的理解：

![](/assets/hiber-15-4.png)

```
	@Test
	public void testUpdateTimeStampCache(){
		Query query = session.createQuery("FROM Employee");
		query.setCacheable(true);
		
		List<Employee> emps = query.list();
		System.out.println(emps.size());
		
		Employee employee = (Employee) session.get(Employee.class, 100);
		employee.setSalary(30000);
		//更改之后，此时根据时间戳来重新决定是否发送select语句访问数据库
		emps = query.list();
		System.out.println(emps.size());
	}
```



