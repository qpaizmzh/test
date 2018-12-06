# 14.通用标签（一）

1.Struts2自动把Action对象放入到值栈中

1）.放入的时间点为：Struts2终将调用Action类的Action方法，但在调用该方法前：

	-&gt;先创建一个StrutsActionProxy对象

	-&gt;再创建StrutsActionProxy之后，对其初始化时，把Action对象放入了值栈中

![](/assets/14-1.png)![](/assets/14-2.png)

```java
  <%--指定的URL包含请求参数--%>
  <s:url value="index.jsp" var="url1">
    <s:param name="pro" value="2002"></s:param>
  </s:url>

  <%--标签的value值会自动通过OGNL获取值栈的值，如果只是想在value传字符串--%>
  <s:url value="index.jsp" var="url1">
    <s:param name="pro" value="productID"></s:param>
  </s:url>

  <%--如果只是想在value传字符串，可以加上单引号--%>
  <s:url value="index.jsp" var="url1">
    <s:param name="pro" value="'productID'"></s:param>
  </s:url>


  <%--获取一个Action地址--%>
  <s:url action="test" var="url1" namespace="/" method="execute">
  </s:url>

  <%-- 可以接受某个提交表单的属性url--%>
  <s:url value="testurl" var="url1" includeParams="all">
  </s:url>
```









