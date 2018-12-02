### ActionSupport

* ActionSupport 是默认的 Action 类: 若某个 action 节点没有配置 class 属性, 则 ActionSupport 即为
  待执行的 Action 类. 而 execute 方法即为要默认执行的 action 方法

```
<action name="testActionSupport">
	<result>/testActionSupport.jsp</result>
</action>
```

等同于：

```
<action name="testActionSupport"
	class="com.opensymphony.xwork2.ActionSupport"
	method="execute">
	<result>/testActionSupport.jsp</result>
</action>
```

## Result：

* result 是 action 节点的子节点
* result 代表 action 方法执行后, 可能去的一个目的地
* 一个 action 节点可以配置多个 result 子节点. 
*  result 的 name 属性值对应着 action 方法可能有的一个返回值. 

```
<result name="index">/index.jsp</result>//name属性对应的是方法返回的字符串的值
```

*  result 一共有 2 个属性, 还有一个是 type: 表示结果的响应类型
* result 的 type 属性值在 struts-default 包的 result-types 节点的 name 属性中定义.
  常用的有 ：

        &gt;dispatcher\(默认的\): 转发. 同 Servlet 中的转发. 

        &gt;redirect: 重定向

        &gt;redirectAction: 重定向到一个 Action

             注意: 通过 redirect 的响应类型也可以便捷的实现 redirectAction 的功能!

```
<result name="index" type="redirectAction">
		<param name="actionName">testAction</param>
		<param name="namespace">/atguigu</param>
	</result>
```

或者是：

```
<result name="index" type="redirect">/atguigu/testAction.do</result>
```

        &gt;chain: 转发到一个 Action 

          注意: 不能通过 type=dispatcher 的方式转发到一个 Action

          只能是:

```
<result name="test" type="chain">
		<param name="actionName">testAction</param>
		<param name="namespace">/atguigu</param>
	</result>
```

         不能是：

```
<result name="test">/atguigu/testAction.do</result>
```

         



