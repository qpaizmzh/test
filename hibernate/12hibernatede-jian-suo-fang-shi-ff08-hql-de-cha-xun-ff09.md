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
               .setEntity(2, dept);//还可以设置实体类的实例来作为参数

          //3. 执行查询
          List<Employee> emps = query.list();
          System.out.println(emps.size());  
      }
  ```

  ![](/assets/hiber-12-5.png)

* 代码例子：

  ```
      //分页查询
      @Test
      public void testPageQuery(){
          String hql = "FROM Employee";
          Query query = session.createQuery(hql);

          int pageNo = 22;
          int pageSize = 5;

          List<Employee> emps = 
                                  query.setFirstResult((pageNo - 1) * pageSize)
                                       .setMaxResults(pageSize)
                                       .list();
          System.out.println(emps);
      }
  ```

![](/assets/hiber-12-6.png)

* 代码例子

  ```
  //这是某个实体类的配置文件，要想在其他地方配置HQL语句的话，就在这个XML文件进行配置
  <?xml version="1.0"?>
  <!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
  "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

  <hibernate-mapping>

      <class name="com.atguigu.hibernate.entities.Employee" table="GG_EMPLOYEE">

          <!--  
          <cache usage="read-write"/>
          -->

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
      //在XML文件添加HQL语句的时候，大于小于号容易产生歧义，实际上是放在CDATA的标签进行书写
      <query name="salaryEmps"><![CDATA[FROM Employee e WHERE e.salary > :minSal AND e.salary < :maxSal]]></query>

  </hibernate-mapping>
  ```

  ```
  运行方法：
  @Test
      public void testNamedQuery(){
          Query query = session.getNamedQuery("salaryEmps");

          List<Employee> emps = query.setFloat("minSal", 5000)
                                     .setFloat("maxSal", 10000)
                                     .list();

          System.out.println(emps.size()); 
      }
  ```

* 投影查询  
  ![](/assets/hiber-12-7.png)

代码例子：

```
//这里的方法乜有直接使用的是Object数组来储存
@Test
    public void testFieldQuery(){
        String hql = "SELECT e.email, e.salary, e.dept FROM Employee e WHERE e.dept = :dept";
        Query query = session.createQuery(hql);

        Department dept = new Department();
        dept.setId(80);
        List<Object[]> result = query.setEntity("dept", dept)
                                     .list();

        for(Object [] objs: result){
            System.out.println(Arrays.asList(objs));
        }
    }
```

```
    @Test
    public void testFieldQuery2(){
            //如果想按照对象的方式来，要在语句书写实体类的同时，要在实体类中添加相关的构造器，参数的顺序要和HQL语句的一致
        String hql = "SELECT new Employee(e.email, e.salary, e.dept) "
                + "FROM Employee e "
                + "WHERE e.dept = :dept";
        Query query = session.createQuery(hql);

        Department dept = new Department();
        dept.setId(80);
        //这样获取的既不需要用Obeject数组包装，直接使用对象进行使用
        List<Employee> result = query.setEntity("dept", dept)
                                     .list();

        for(Employee emp: result){
            System.out.println(emp.getId() + ", " + emp.getEmail() 
                    + ", " + emp.getSalary() + ", " + emp.getDept());
        }
    }
```

![](/assets/hiber-12-8.png)代码的例子：

```
    @Test
    public void testGroupBy(){
        String hql = "SELECT min(e.salary), max(e.salary) "
                + "FROM Employee e "
                + "GROUP BY e.dept "
                + "HAVING min(salary) > :minSal";

        Query query = session.createQuery(hql)
                             .setFloat("minSal", 8000);

        List<Object []> result = query.list();
        for(Object [] objs: result){
            System.out.println(Arrays.asList(objs));
        }
    }
```

![](/assets/hiber-12-9.png)代码例子：

```
@Test
    public void testLeftJoinFetch(){
        //这条语句可以直接使用左连接，将查询的对象中某个属性关联的对象也一并查询出来
        //因为是左外连接的原因，所以会用重复的情况，即使副表的关联为空，同样也会将数据查出来
//        String hql = "SELECT DISTINCT d FROM Department d LEFT JOIN FETCH d.emps";

       //另一个去重复的方式就是先使用LinkedHashSet来将数据去重，然后再进行list的包装
        String hql = "FROM Department d INNER JOIN FETCH d.emps";
        Query query = session.createQuery(hql);

        List<Department> depts = query.list();
        //包装去重的示例
        depts = new ArrayList<>(new LinkedHashSet(depts));
        System.out.println(depts.size()); 

        for(Department dept: depts){
            System.out.println(dept.getName() + "-" + dept.getEmps().size());
        }
    }
```

```
    @Test
    public void testLeftJoin(){
        //可以直接使用语句获取对象，但是这样的获取对象没有加fetch这种的话是不会初始化对应的对象，
        //实际上要等到使用的时候，hibernate才会将关联的对象进行查询
        String hql = "SELECT DISTINCT d FROM Department d LEFT JOIN d.emps";
        Query query = session.createQuery(hql);

        List<Department> depts = query.list();
        System.out.println(depts.size());

        for(Department dept: depts){
            //这里每一次的调用，都会查询getEmps中对应的表，获取了实例之后才会继续进行后面的方法
            System.out.println(dept.getName() + ", " + dept.getEmps().size()); 
        }
        //这里只使用了left join，并没有使用fetch关键字，这样实际上在HQL语句里面拿的就是两个字段，
        //只会返回object数组

        //String hql = "SELECT d.abc,d.emps d FROM Department d LEFT JOIN d.emps";
//        List<Object []> result = query.list(); 
//        result = new ArrayList<>(new LinkedHashSet<>(result));
//        System.out.println(result); 
//        
//        for(Object [] objs: result){
//            System.out.println(Arrays.asList(objs));
//        }
    }
```

![](/assets/hiber-12-10.png)

* 代码例子：
  ```
  @Test
  	public void testLeftJoinFetch2(){
  		String hql = "SELECT e FROM Employee e INNER JOIN e.dept";
  		Query query = session.createQuery(hql);
		
  		List<Employee> emps = query.list();
  		System.out.println(emps.size()); 
		
  		for(Employee emp: emps){
  			System.out.println(emp.getName() + ", " + emp.getDept().getName());
  		}
  	}
  ```





