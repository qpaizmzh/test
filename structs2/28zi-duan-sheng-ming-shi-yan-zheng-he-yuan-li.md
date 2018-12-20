# 28.字段声明式验证和原理

---

1. Struts2 的验证

1\). 验证分为两种:

	&gt; 声明式验证\*

		&gt;&gt; 对哪个 Action 或 Model 的那个字段进行验证

		&gt;&gt; 使用什么验证规则

		&gt;&gt; 如果验证失败, 转向哪一个页面, 显示是什么错误消息

	&gt; 编程式验证

2\). 声明式验证的 helloworld

I.  先明确对哪一个 Action 的哪一个字段进行验证: age

II. 编写配置文件: 

	&gt; 把 struts-2.3.15.3\apps\struts2-blank\WEB-INF\classes\example 下的 Login-validation.xml 文件复制到

	当前 Action 所在的包下. 

	&gt; 把该配置文件改为: 把  Login 改为当前 Action 的名字. 

	&gt; 编写验证规则: 参见 struts-2.3.15.3/docs/WW/docs/validation.html 文档即可.

	&gt; 在配置文件中可以定义错误消息: 

```
	<field name="age">
         <field-validator type="int">
             <param name="min">20</param>
             <param name="max">50</param>
             <message>^^Age needs to be between ${min} and ${max}</message>
         </field-validator>
     </field>
```

     

     &gt; 该错误消息可以国际化吗. 可以

     &lt;message key="error.int"&gt;&lt;/message&gt;. 

            再在国际化资源文件 中加入一个键值对: error.int=^^^Age needs to be between ${min} and ${max}

	

III. 若验证失败, 则转向 input 的那个 result. 所以需要配置 name=input 的 result

	 &lt;result name="input"&gt;/validation.jsp&lt;/result&gt;

	 

IV. 如何显示错误消息呢 ? 	 

	&gt; 若使用的是非 simple, 则自动显示错误消息.

	&gt; 若使用的是 simple 主题, 则需要 s:fielderror 标签或直接使用 EL 表达式\(使用 OGNL\)

	

	${fieldErrors.age\[0\] } 

	OR

	&lt;s:fielderror fieldName="age"&gt;&lt;/s:fielderror&gt;\*



3\). 注意: 若一个 Action 类可以应答多个 action 请求, 多个 action 请求使用不同的验证规则, 怎么办 ?



	&gt; 为每一个不同的 action 请求定义其对应的验证文件: ActionClassName-AliasName-validation.xml

	&gt; 不带别名的配置文件: ActionClassName-validation.xml 中的验证规则依然会发生作用. 可以把各个 action 公有的验证规则

	配置在其中. 但需要注意的是, 只适用于某一个 action 的请求的验证规则就不要这里再配置了. 

	

4\). 声明式验证框架的原理:

	&gt; Struts2 默认的拦截器栈中提供了一个 validation 拦截器

	&gt; 每个具体的验证规则都会对应具体的一个验证器. 有一个配置文件把验证规则名称和验证器关联起来了. 而实际上验证的是那个验证器. 

	该文件位于 com.opensymphony.xwork2.validator.validators 下的 default.xml

```
<validator name="required" class="com.opensymphony.xwork2.validator.validators.RequiredFieldValidator"/>
```



