# 创建Struts2的简单步骤和原理

---

# 

* 由 product-input.action 转到 /WEB-INF/pages/input.jsp：

```java
<action name="product-input">
	<result>/WEB-INF/pages/input.jsp</result>
</action>
```



* 由 input.jsp 页面的 action: product-save.action 到 Product's save, 再到  /WEB-INF/pages/details.jsp:

```java
	<action name="product-save" class="com.atguigu.struts2.helloworld.Product"
		method="save">
		<result name="details">/WEB-INF/pages/details.jsp</result>	
	</action>
```

* 在 Prodcut 中定义一个 save 方法, 且返回值为 details:

![](/assets/struts-save.png)



