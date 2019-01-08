# 继承映射关系（了解用）

![](/assets/hiber-10-1.png)

![](/assets/hiber-10-2.png)![](/assets/hiber-10-3.png)例子：

person属性：

```
	private Integer id;
	private String name;
	private int age;
```

student属性

```
	private String school;
```

Person的xml:

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="com.atguigu.hibernate.subclass">

    <class name="Person" table="PERSONS" discriminator-value="PERSON">

        <id name="id" type="java.lang.Integer">
            <column name="ID" />
            <generator class="native" />
        </id>
        
        <!-- 配置辨别者列 -->
		<discriminator column="TYPE" type="string"></discriminator>

        <property name="name" type="java.lang.String">
            <column name="NAME" />
        </property>
        
        <property name="age" type="int">
            <column name="AGE" />
        </property>
        
        <!-- 映射子类 Student, 使用 subclass 进行映射 -->
        <subclass name="Student" discriminator-value="STUDENT">
        	<property name="school" type="string" column="SCHOOL"></property>
        </subclass>
        
    </class>
</hibernate-mapping>

```

![](/assets/hiber-10-4.png)person、student属性不变：

person.xml

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="com.atguigu.hibernate.joined.subclass">

    <class name="Person" table="PERSONS">

        <id name="id" type="java.lang.Integer">
            <column name="ID" />
            <generator class="native" />
        </id>
        
        <property name="name" type="java.lang.String">
            <column name="NAME" />
        </property>
        
        <property name="age" type="int">
            <column name="AGE" />
        </property>
        
        <joined-subclass name="Student" table="STUDENTS">
        	<key column="STUDENT_id"></key>
        	<property name="school" type="string" column="SCHOOL"></property>
        </joined-subclass>
        
    </class>
</hibernate-mapping>

```

![](/assets/hiber-10-5.png)person、student类属性不变

person.xml

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="com.atguigu.hibernate.union.subclass">

    <class name="Person" table="PERSONS">

        <id name="id" type="java.lang.Integer">
            <column name="ID" />
            <generator class="hilo" />
        </id>
        
        <property name="name" type="java.lang.String">
            <column name="NAME" />
        </property>
        
        <property name="age" type="int">
            <column name="AGE" />
        </property>
	
		<union-subclass name="Student" table="STUDENTS">
			<property name="school" column="SCHOOL" type="string"></property>
		</union-subclass>        
        
    </class>
</hibernate-mapping>

```

![](/assets/hiber-10-6.png)

