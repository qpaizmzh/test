# 12.ONGL的使用方法

1. 关于值栈:

   1\). helloWorld 时, ${productName} 读取 productName 值, 实际上该属性并不在 request 等域对象中,        而是从值栈中获取的. 

   2\). ValueStack: 

        I.  可以从 ActionContext 中获取值栈对象

        II. 值栈分为两个逻辑部分

	      &gt; Map 栈: 实际上是 OgnlContext 类型, 是个 Map, 也是对 ActionContext 的一个引用. 里边保存着各种 Map:

	         requestMap, sessionMap, applicationMap, parametersMap, attr  

	       &gt; 对象栈: 实际上是 CompoundRoot 类型, 是一个使用 ArrayList 定义的栈. 里边保存各种和当前 Action 实例相关的对象.是一个数据结构意义的栈.

	                   

 2. Struts2 利用 s:property 标签和 OGNL 表达式来读取值栈中的属性值

 1\). 值栈中的属性值:

 	&gt; 对于对象栈: 对象栈中某一个对象的属性值

 	&gt; Map 栈: request, session, application 的一个属性值 或 一个请求参数的值. 

 2\). 读取对象栈中对象的属性:

 	&gt; 若想访问 Object Stack 里的某个对象的属性. 可以使用以下几种形式之一: 

	  object.propertyName ; object\['propertyName'\] ; object\["propertyName"\]	

	&gt; ObjectStack 里的对象可以通过一个从零开始的下标来引用. ObjectStack 里的栈顶对象可以用 \[0\] 来引用, 它下面的那个对象可以用 \[1\] 引用. 

	  \[0\].message   

	&gt; \[n\] 的含义是从第 n 个开始搜索, 而不是只搜索第 n 个对象

	&gt; 若从栈顶对象开始搜索, 则可以省略下标部分: message 

	&gt; 结合 s:property 标签: &lt;s:property value="\[0\].message" /&gt;  &lt;s:property value="message" /&gt;

 3\). 默认情况下, Action 对象会被 Struts2 自动的放到值栈的栈顶. 





3.简单的例子：

①.访问ContextMap中的域对象的属性：

```
     ${sessionScope.对象类名.对象属性}
     <s:property value='#session.对象类名.对象属性'>//他们是等价的，其他的request等域对象也一样
```

![](/assets/12-1.png)

②.调用public静态字段和public方法

```
   <s:property value='@java.lang.Math@pi'>
   <s:property value='@java.lang.Math@cos(0)'>//前提是要在Struts.xml打开静态方法调用的接口：
   
   //struts.xml的添加标签，打开调用静态方法的借口，默认情况是不可以打开的
<constant name="struts.ognl.allowStaticMethodAccess" value="true"></constant>
```



③.调用对象栈的方法为一个属性赋值：

```
<s:property value = 'set属性名('值')'/>//设置该值
<s:property value = '属性名'/>//获取该值
```

④.访问数组的类型：

```
<%
    String[] names = new String[]{"1","2"};
    request.setAttribute("names",names);
%>
    length:<s:property value="#attr.names.length">
    names[2]:<s:property value="#attr.names[2]">
```

⑤.访问List的类型：

![](/assets/12-3.png)

⑥.访问Map的类型：![](/assets/12-2.png)























