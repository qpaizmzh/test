# Hibernate的简单介绍和简单的对象映射方法

![](/assets/hiber-1.png)![](/assets/hiber-2.png)

![](/assets/hiber-3.png)

for instance（idea）:

* 创建需要映射的表，便按照教程将sql进行连接：
* 创建hibernate的项目，并将hibernate中的hibernate.cfg.xml中相关信息配置好

```
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
        "-//Hibernate/Hibernate Configuration DTD//EN"
        "http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!--这里要在URL中添加?serverTimezone=UTC才能正常运行，原因是数据库连接做了相关验证-->
        <property name="connection.url">jdbc:mysql://localhost:3306/hiber?serverTimezone=UTC</property>
        <property name="connection.driver_class">com.mysql.cj.jdbc.Driver</property>
        <!--不太了解选的不同方言会有什么效果-->
        <property name="dialect">org.hibernate.dialect.MySQL57Dialect</property>


        <property name="connection.username">root</property>
        <property name="connection.password">085436</property>
        <!--格式化sql语句，同时在命令太显示sql语句，方便阅读-->
        <property name="format_sql">true</property>
        <property name="show_sql">true</property>

        <!--用来映射数据库表和实体类关系的属性标签，格式像如下-->
        <mapping class="com.hibernate.hello.ArtiEntity"/>
        <mapping resource="com/hibernate/hello/ArtiEntity.hbm.xml"/>
        <!-- <property name="connection.username"/> -->
        <!-- <property name="connection.password"/> -->

        <!-- DB schema will be updated if needed -->
        <!-- <property name="hbm2ddl.auto">update</property> -->
    </session-factory>
</hibernate-configuration>
```



