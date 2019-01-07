1对1主外键映射![](/assets/hiber-8-1.png)![](/assets/hiber-8-2.png)

* 1对1外键映射

Manager.xml

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>

    <class name="com.hibernate.hello.ManagerEntity" table="manager" schema="hiber">
        <id name="idmanager" column="idmanager">
            <generator class="native"></generator>
        </id>
        <property name="managerName" column="managerName"/>
        //设置一对一的时候，要注意添加property-ref属性，而且使用这个标签的时候另一端必须有<manyToOne>的标签
        <one-to-one name="departmentEntity"  class="com.hibernate.hello.DepartmentEntity" property-ref="managerEntity"></one-to-one>
    </class>
</hibernate-mapping>
```

Department.xml

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping>

    <class name="com.hibernate.hello.DepartmentEntity" table="department" schema="hiber">
        <id name="iddepartment" column="iddepartment">
            <generator class="native"></generator>
        </id>
        <property name="departmentName" column="departmentName"/>
        //注意的地方还是添加唯一约束，就可以变成一对一的标签
        <many-to-one name="managerEntity" class="com.hibernate.hello.ManagerEntity" 
        column="manager_id" unique="true"></many-to-one>
    </class>
</hibernate-mapping>
```

ps:实体类要互相添加对方实体类的应用作为属性

* 1对1使用主键

![](/assets/hiber-8-4.png)代码例子：

首先实体类是同样只有一端添加对方实体类引用的属性

Mangager.xml

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>

    <class name="com.atguigu.hibernate.one2one.primary.Manager" table="MANAGERS">
        <id name="mgrId" type="java.lang.Integer">
            <column name="MGR_ID" />
            <generator class="native" />
        </id>

        <property name="mgrName" type="java.lang.String">
            <column name="MGR_NAME" />
        </property>
        
            //这个标签在利用映射1对1的时候并没有起到作用，不是必须添加的项    
        <one-to-one name="dept" 
            class="com.atguigu.hibernate.one2one.primary.Department"></one-to-one>

    </class>

</hibernate-mapping>
```

Department.xml

```
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
"http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.atguigu.hibernate.one2one.primary">

    <class name="Department" table="DEPARTMENTS">

        <id name="deptId" type="java.lang.Integer">
            <column name="DEPT_ID" />
            <!-- 使用外键的方式来生成当前的主键 -->
            <generator class="foreign">
                <!-- property 属性指定使用当前持久化类的哪一个属性的主键作为外键 -->
                <param name="property">mgr</param>
            </generator>
        </id>

        <property name="deptName" type="java.lang.String">
            <column name="DEPT_NAME" />
        </property>

        <!--  
        采用 foreign 主键生成器策略的一端增加 one-to-one 元素映射关联属性,
        其 one-to-one 节点还应增加 constrained=true 属性, 以使当前的主键上添加外键约束
        -->
        <one-to-one name="mgr" class="Manager" constrained="true"></one-to-one>

    </class>
</hibernate-mapping>
```

![](/assets/hiber-8-5.png)

