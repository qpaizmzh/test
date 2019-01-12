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



