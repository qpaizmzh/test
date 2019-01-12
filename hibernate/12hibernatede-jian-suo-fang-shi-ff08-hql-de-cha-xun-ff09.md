# hibernate的检索方式（HQL的查询）

![](/assets/hiber-12-1.png)![](/assets/hiber-12-2.png)![](/assets/hiber-12-3.png)

* 例子：
  ```
  @Test
  	public void testHQLNamedParameter(){
		
  		//1. 创建 Query 对象
  		//基于命名参数. 在想要设置的参数里面添加"：参数名"来设置定的参数值
  		String hql = "FROM Employee e WHERE e.salary > :sal AND e.email LIKE :email";
  		Query query = session.createQuery(hql);
		
  		//2. 绑定参数
  		query.setFloat("sal", 7000)
  		     .setString("email", "%A%");
		
  		//3. 执行查询
  		List<Employee> emps = query.list();
  		System.out.println(emps.size());  
  	}
	
  	@Test
  	public void testHQL(){
		
  		//1. 创建 Query 对象
  		//基于位置的参数. 
  		String hql = "FROM Employee e WHERE e.salary > ? AND e.email LIKE ? AND e.dept = ? "
  				+ "ORDER BY e.salary";
  		Query query = session.createQuery(hql);
		
  		//2. 绑定参数
  		//Query 对象调用 setXxx 方法支持方法链的编程风格.
  		Department dept = new Department();
  		dept.setId(80); 
  		query.setFloat(0, 6000)
  		     .setString(1, "%A%")
  		     .setEntity(2, dept);
		
  		//3. 执行查询
  		List<Employee> emps = query.list();
  		System.out.println(emps.size());  
  	}
  ```



