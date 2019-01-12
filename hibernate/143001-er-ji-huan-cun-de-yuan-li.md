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



