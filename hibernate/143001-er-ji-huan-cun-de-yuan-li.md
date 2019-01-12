# 二级缓存的原理

![](/assets/hiber-14-1.png)![](/assets/hibernate-14-2.png)![](/assets/hibernate-14-3.png)

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



