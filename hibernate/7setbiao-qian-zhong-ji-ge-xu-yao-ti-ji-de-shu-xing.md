# set标签中几个需要提及的属性

![](/assets/hiber-7-1.png)![](/assets/hiber-7-2.png)例子：

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>

    <class name="com.hibernate.hello.Customer" table="customer">
        <id name="customerId" column="idcustomer">
            <generator class="native"></generator>
        </id>
        <property name="customerName" column="customer_name"/>
        //在set标签里添加inverse属性，可以减少sql语句的访问
        <set name="orderSet" table="orders" inverse="true">
            <key column="customerid"></key>
            <one-to-many class="com.hibernate.hello.Order"></one-to-many>
        </set>
    </class>
</hibernate-mapping>
```

![](/assets/hiber-7-3.png)例子：

```
  
  //Customer.hbm.xml配置文件
  <?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>

    <class name="com.hibernate.hello.Customer" table="customer">
        <id name="customerId" column="idcustomer">
            <generator class="native"></generator>
        </id>
        <property name="customerName" column="customer_name"/>
        //在set标签里添加cascade属性
        <set name="orderSet" table="orders" cascade="delete-orphan">
            <key column="customerid"></key>
            <one-to-many class="com.hibernate.hello.Order"></one-to-many>
        </set>
    </class>
</hibernate-mapping>
  
  
  //单元测试的方法
    @Test
    public void cascade(){
        Customer customer = (Customer) session.get(Customer.class, 16);
        //设置了cascade为delete的时候，除了cutomer被删除，其他的关联对象也在数据库被删
        //session.delete(customer);
        //设置了cascade为delete-orphan的话，会删除所有和customer有关的对象（不包括customer）
        customer.getOrderSet().clear();
    }


    @Test
    public void delete() {
        Customer customer = (Customer) session.get(Customer.class, 13);
        //有其他关联对象的话，delete方法执行就会爆出异常
        session.delete(customer);
    }
```



