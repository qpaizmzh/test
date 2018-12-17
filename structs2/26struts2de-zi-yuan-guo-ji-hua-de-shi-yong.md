# 26.Struts2的资源国际化的使用

![](/assets/26-1.png)![](/assets/27-2.png)![](/assets/26-3.png)![](/assets/26-4.png)![](/assets/26-5.png)![](/assets/26-6.png)

1. 国际化的目标

1\). 如何配置国际化资源文件

I.   Action 范围资源文件: 在Action类文件所在的路径建立名为 ActionName\_language\_country.properties 的文件

II.  包范围资源文件: 在包的根路径下建立文件名为 package\_language\_country.properties 的属性文件,

一旦建立，处于该包下的所有 Action 都可以访问该资源文件。注意：包范围资源文件的 baseName 就是package，不是Action所在的包名。

III. 全局资源文件

```
&gt; 命名方式: basename\_language\_country.properties

&gt; struts.xml &lt;constant name="struts.custom.i18n.resources" value="baseName"/&gt;
```

IV.  国际化资源文件加载的顺序如何呢 ? 离当前 Action 较近的将被优先加载.

假设我们在某个 ChildAction 中调用了getText\("username"\)：

\(1\) 加载和 ChildAction 的类文件在同一个包下的系列资源文件 ChildAction.properties

\(2\) 加载  ChildAction 实现的接口 IChild，且和 IChildn 在同一个包下 IChild.properties 系列资源文件。

\(3\) 加载 ChildAction 父类 Parent，且和 Parent 在同一个包下的 baseName 为 Parent.properties 系列资源文件。

\(4\) 若 ChildAction 实现 ModelDriven 接口，则对于getModel\(\)方法返回的model 对象，重新执行第\(1\)步操作。

\(5\) 查找当前包下 package.properties 系列资源文件。

\(6\) 沿着当前包上溯，直到最顶层包来查找 package.properties 的系列资源文件。

\(7\) 查找 struts.custom.i18n.resources 常量指定 baseName 的系列资源文件。

\(8\) 直接输出该key的字符串值。

2\). 如何在页面上 和 Action 类中访问国际化资源文件的  value 值

I. 在 Action 类中. 若 Action 实现了 TextProvider 接口, 则可以调用其 getText\(\) 方法获取 value 值

```
&gt; 通过继承 ActionSupport 的方式。 
```

II. 页面上可以使用 s:text 标签; 对于表单标签可以使用表单标签的 key 属性值

```
&gt; 若有占位符, 则可以使用 s:text 标签的 s:param 子标签来填充占位符

&gt; 可以利用标签和 OGNL 表达式直接访问值栈中的属性值\(对象栈 和 Map 栈\)



time=Time:{0}



&lt;s:text name="time"&gt;

    &lt;s:param value="date"&gt;&lt;/s:param&gt;

&lt;/s:text&gt;



------------------------------------



time2=Time:${date}



&lt;s:text name="time2"&gt;&lt;/s:text&gt;
```

3\). 实现通过超链接切换语言.

I.  关键之处在于知道 Struts2 框架是如何确定 Local 对象的 !

II. 可以通过阅读 I18N 拦截器知道.

III. 具体确定 Locale 对象的过程:

```
&gt; Struts2 使用 i18n 拦截器 处理国际化，并且将其注册在默认的拦截器栈中

&gt; i18n拦截器在执行Action方法前，自动查找请求中一个名为 request\_locale 的参数。

      如果该参数存在，拦截器就将其作为参数，转换成Locale对象，并将其设为用户默认的Locale\(代表国家/语言环境\)。

      并把其设置为 session 的 WW\_TRANS\_I18N\_LOCALE 属性

&gt; 若 request 没有名为request\_locale 的参数，则 i18n 拦截器会从 Session 中获取 WW\_TRANS\_I18N\_LOCALE 的属性值，

     若该值不为空，则将该属性值设置为浏览者的默认Locale 

&gt; 若 session 中的 WW\_TRANS\_I18N\_LOCALE 的属性值为空，则从 ActionContext 中获取 Locale 对象。
```

IV.  具体实现: 只需要在超连接的后面附着  request\_locale 的请求参数, 值是 语言国家 代码.

```
&lt;a href="testI18n.action?request\_locale=en\_US"&gt;English&lt;/a&gt;

&lt;a href="testI18n.action?request\_locale=zh\_CN"&gt;中文&lt;/a&gt;



&gt; 注意: 超链接必须是一个 Struts2 的请求, 即使 i18n 拦截器工作!
```

### 

### 源代码

---

```java
 <%@page import="java.util.Date"%>
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@taglib prefix="s" uri="/struts-tags" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>

    <s:debug></s:debug>

    //这里是通过超链接的方式将目前的
    <a href="testI18n.action?request_locale=en_US">English</a>
    <a href="testI18n.action?request_locale=zh_CN">中文</a>
    <br><br>

    <a href="index.jsp">Index Page</a>

    <br><br>

    <s:text name="time">
        <s:param value="date"></s:param>
    </s:text>

    <br><br>

    <s:text name="time2"></s:text>
    <br><br>


    <s:form action="" theme="simple">

        <!-- 
            若 label 标签使用 %{getText('username')} 的方式就也可以从国际化资源文件中获取 value 值了 
            因为此时在对象栈中有 DefaultTextProvider 的一个实例, 该对象中提供了访问国际化资源文件的 getText() 方法
            同时还需要通知 struts2 框架 label 中放入的不再是一个普通的字符串, 而是一个 OGNL 表达式. 所以使用 %{} 把 getText()
            包装起来, 以强制进行 OGNL 解析. 
        -->
        <!--  
            页面上可以直接使用 <s:text name="" /> 标签来访问国际化资源文件里的 value 值. 
        -->
        <s:text name="username"/>:<s:textfield name="username" label="%{getText('username')}"></s:textfield>

        <!-- key 的方式是直接上资源文件中获取 value 值 -->
        <s:text name="username"/>:<s:textfield name="username" key="username"></s:textfield>
        <s:text name="password"/>:<s:password name="password" key="password"></s:password>

        <s:submit key="submit" value="%{getText('submit')}"></s:submit>

    </s:form>

</body>
</html>
```

在Action中主要获取字段值的方法是：

```java
package com.atguigu.struts2.i18n.app;

import java.util.Arrays;
import java.util.Date;

import com.opensymphony.xwork2.ActionSupport;

public class TestI18nAction extends ActionSupport {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;
	
	private Date date = null;
	
	public Date getDate() {
		return date;
	}

	public void setDate(Date date) {
		this.date = date;
	}

	@Override
	public String execute() throws Exception {
		
		date = new Date();
		
		//1. 在 Action 中访问国际化资源文件的 value 值
		String username = getText("username");
		System.out.println(username);
		
		//2. 带占位符的
		String time = getText("time", Arrays.asList(date));
		System.out.println(time);
		
		return SUCCESS;
	}
}

```

properties的文件代码示例如下：

```
username=UserName
passwword=Password
submit=Submit

time=Time:{0}
time2=Time:${date}
```





