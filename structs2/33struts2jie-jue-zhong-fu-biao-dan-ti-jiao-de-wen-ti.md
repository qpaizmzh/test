# 33.Struts2.解决重复表单提交的问题

![](/assets/33-1.png)![](/assets/33-2.png)3. 表单的重复提交问题



1\). 什么是表单的重复提交



	&gt; 在不刷新表单页面的前提下: 

		&gt;&gt; 多次点击提交按钮

		&gt;&gt; 已经提交成功, 按 "回退" 之后, 再点击 "提交按钮".

		&gt;&gt; 在控制器响应页面的形式为转发情况下，若已经提交成功, 然后点击 "刷新\(F5\)"

		

	&gt; 注意:

		&gt;&gt; 若刷新表单页面, 再提交表单不算重复提交

		&gt;&gt; 若使用的是 redirect 的响应类型, 已经提交成功后, 再点击 "刷新", 不是表单的重复提交

		

2\). 表单重复提交的危害: 数据多次传输，给 			

3\). Struts2 解决表单的重复提交问题:



I. 在 s:form 中添加 s:token 子标签

&gt; 生成一个隐藏域

&gt; 在 session 添加一个属性值

&gt; 隐藏域的值和 session 的属性值是一致的. 

	

II. 使用 Token 或 TokenSession 拦截器. 

&gt; 这两个拦截器均不在默认的拦截器栈中, 所以需要手工配置一下

&gt; 若使用 Token 拦截器, 则需要配置一个 token.valid 的 result

&gt; 若使用 TokenSession 拦截器, 则不需要配置任何其它的 result

	

III. Token VS TokenSession

&gt; 都是解决表单重复提交问题的

&gt; 使用 token 拦截器会转到 token.valid 这个 result

&gt; 使用 tokenSession 拦截器则还会响应那个目标页面, 但不会执行 tokenSession 的后续拦截器. 就像什么都没发生过一样!

	

IV. 可以使用 s:actionerror 标签来显示重复提交的错误消息. 

该错误消息可以在国际化资源文件中覆盖. 该消息可以在 struts-messages.properties 文件中找到

struts.messages.invalid.token=^^The form has already been processed or no token was supplied, please try again.

