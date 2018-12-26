# 34.自定义拦截器

![](/assets/34-1.png)![](/assets/34-2.png)

---

4. 自定义拦截器



1\). 具体步骤

I. 定义一个拦截器的类

&gt; 可以实现 Interceptor 接口

&gt; 继承 AbstractInterceptor 抽象类



II. 在 struts.xml 文件配置.	



	`<interceptors>`

`	<interceptor name="hello" class="com.atguigu.struts2.interceptors.MyInterceptor"></interceptor>`

`	</interceptors>`

`	<action name="testToken" class="com.atguigu.struts2.token.app.TokenAction">`

`		<interceptor-ref name="hello"></interceptor-ref>`

`		<interceptor-ref name="defaultStack"></interceptor-ref>`

`		<result>/success.jsp</result>`

`		<result name="invalid.token">/token-error.jsp</result>`

`	</action>`

	

III. 注意: 在自定义的拦截器中可以选择不调用 ActionInvocation 的 invoke\(\) 方法. 那么后续的拦截器和 Action 方法将不会被调用.

Struts 会渲染自定义拦截器 intercept 方法返回值对应的 result

例子：

创建的拦截器的类：

```
package com.atguigu.struts2.interceptors;

import com.opensymphony.xwork2.ActionInvocation;
import com.opensymphony.xwork2.interceptor.AbstractInterceptor;

public class MyInterceptor extends AbstractInterceptor {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1L;

	@Override
	public String intercept(ActionInvocation invocation) throws Exception {
		
		System.out.println("before invocation.invoke...");
		
		//String result = invocation.invoke();
		
		System.out.println("after invocation.invoke...");
		
		return "success";
	}

}

```

struts.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC
	"-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
	"http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>

	
	<!-- 配置国际化资源文件 -->
	<constant name="struts.custom.i18n.resources" value="i18n"></constant>
	
    <package name="default" namespace="/" extends="struts-default">
		
		<interceptors>
			//创建的拦截器的配置方式
			<interceptor name="hello" class="com.atguigu.struts2.interceptors.MyInterceptor"></interceptor>
		
			<interceptor-stack name="atguigustack">
				<interceptor-ref name="defaultStack">
					<param name="fileUpload.maximumSize">2097152</param>
					<!--  	
					<param name="fileUpload.allowedTypes">text/html,text/xml</param>
					<param name="fileUpload.allowedExtensions">html,dtd,xml</param>
					-->
				</interceptor-ref>
			</interceptor-stack>
			
		</interceptors>
		
		<default-interceptor-ref name="atguigustack"></default-interceptor-ref>
		
		
		<action name="testToken" class="com.atguigu.struts2.token.app.TokenAction">
			<interceptor-ref name="hello"></interceptor-ref>//使用的自定义的拦截器在哪个action
			<interceptor-ref name="tokenSession"></interceptor-ref>
			<interceptor-ref name="defaultStack"></interceptor-ref>
			<result>/success.jsp</result>
			<result name="invalid.token">/token-error.jsp</result>
		</action>
					
    </package>


</struts>

```



