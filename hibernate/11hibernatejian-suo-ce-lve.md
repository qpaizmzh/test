# hibernate检索策略

* 类级别的检索策略
  类级别可选的检索策略包括立即检索和延迟检索，默认为延迟检索  
  -立即检索：立即加载检索方法指定的对象  
  -延迟检索：延迟加载检索方法指定的对象。在使用具体属性的时候，再进行加载

* 类级别的检索策略可以通过设置&lt;class&gt;元素的lazy属性来进行设置

  ```
  <?xml version='1.0' encoding='utf-8'?>
  <!DOCTYPE hibernate-mapping PUBLIC
          "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
          "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
  <hibernate-mapping>
      //直接在这里class标签添加lazy属性就可以,默认是true
      <class name="com.hibernate.MTM.ItemsEntity" table="items" schema="hiber" lazy="true">
          <id name="id" column="ID">
              <generator class="native"></generator>
          </id>
          <property name="name" column="NAME"/>
          <property name="price" column="PRICE"/>
          <set name="catagoriesEntities" table="categories_items">
              <key column="items_ID"></key>
              <many-to-many class="com.hibernate.MTM.CatagoriesEntity" column="categories_ID"></many-to-many>
          </set>
      </class>
  </hibernate-mapping>
  ```



* 无论&lt;class&gt;元素的lazy属性是true还是false，seesion的get\(\)方法及Query的list\(\)方法在类级别总是使用立即检索策略

* 若 &lt;class&gt; 元素的 lazy 属性为 true 或取默认值, Session 的 load\(\) 方法不会执行查询数据表的 SELECT 语句, 仅返回代理类对象的实例, 该代理类实例有如下特征:
  由 Hibernate 在运行时采用 CGLIB 工具动态生成
  Hibernate 创建代理类实例时, 仅初始化其 OID 属性
  在应用程序第一次访问代理类实例的非 OID 属性时, Hibernate 会初始化代理类实例

![](/assets/hiber-11-1.png)![](/assets/hiber-11-2.png)![](/assets/hiber-11-3.png)![](/assets/hiber-11-.png)![](/assets/hiber-11-6.png)![](/assets/hiber-11-7.png)![](/assets/hiber-11-8.png)![](/assets/hiber-11-9.png)![](/assets/hiber-11-10\.png)![](/assets/hiber-11-12.png)

