# QBC检索和本地SQL检索

![](/assets/hiber-13-1.png)

```
	@Test
	public void testHQLUpdate(){
		String hql = "DELETE FROM Department d WHERE d.id = :id";
		//HQL直接删除数据的方法
		session.createQuery(hql).setInteger("id", 280)
		                        .executeUpdate();
	}
	
	@Test
	public void testNativeSQL(){
		//使用本地SQL解决HQL无法使用的一些复杂的SQL语句
		String sql = "INSERT INTO gg_department VALUES(?, ?)";
		Query query = session.createSQLQuery(sql);
		
		query.setInteger(0, 280)
		     .setString(1, "ATGUIGU")
		     .executeUpdate();
	}
	
	@Test
	public void testQBC4(){
		Criteria criteria = session.createCriteria(Employee.class);
		
		//1. 添加排序
		criteria.addOrder(Order.asc("salary"));
		criteria.addOrder(Order.desc("email"));
		
		//2. 添加翻页方法
		int pageSize = 5;
		int pageNo = 3;
		criteria.setFirstResult((pageNo - 1) * pageSize)
		        .setMaxResults(pageSize)
		        .list();
	}
	
	@Test
	public void testQBC3(){
		Criteria criteria = session.createCriteria(Employee.class);
		
		//统计查询: 使用 Projection 来表示: 可以由 Projections 的静态方法得到
		criteria.setProjection(Projections.max("salary"));
		
		System.out.println(criteria.uniqueResult()); 
	}
	
	@Test
	public void testQBC2(){
		Criteria criteria = session.createCriteria(Employee.class);
		
		//1. AND: 使用 Conjunction 表示
		//Conjunction 本身就是一个 Criterion 对象
		//且其中还可以添加 Criterion 对象
		Conjunction conjunction = Restrictions.conjunction();
		conjunction.add(Restrictions.like("name", "a", MatchMode.ANYWHERE));
		Department dept = new Department();
		dept.setId(80);
		conjunction.add(Restrictions.eq("dept", dept));
		System.out.println(conjunction); 
		
		//2. OR
		//or使用Disjunction来表示查询的关系
		Disjunction disjunction = Restrictions.disjunction();
		disjunction.add(Restrictions.ge("salary", 6000F));
		disjunction.add(Restrictions.isNull("email"));
		
		criteria.add(disjunction);
		criteria.add(conjunction);
		
		criteria.list();
	}
	
	@Test
	public void testQBC(){
		//1. 创建一个 Criteria 对象
		Criteria criteria = session.createCriteria(Employee.class);
		
		//2. 添加查询条件: 在 QBC 中查询条件使用 Criterion 来表示
		//Criterion 可以通过 Restrictions 的静态方法得到
		criteria.add(Restrictions.eq("email", "SKUMAR"));
		criteria.add(Restrictions.gt("salary", 5000F));
		
		//3. 执行查询
		Employee employee = (Employee) criteria.uniqueResult();
		System.out.println(employee); 
	}
```



