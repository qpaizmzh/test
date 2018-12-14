# 21.Struts2的CRUD的小结与存在的问题

## 使用paramsPrepareParamsStack 拦截器栈后的运行流程

这个其实是CRUD前面使用了这个参数拦截器之后可以省下了再Action类里面添加私有属性来获取对应的属性值

---

1\). paramsPrepareParamsStack 和 defaultStack 一样都是拦截器栈. 而 struts-default 包默认使用的是defaultStack



2\). 可以在 Struts 配置文件中通过以下方式修改使用的默认的拦截器栈

&lt;default-interceptor-ref name="paramsPrepareParamsStack"&gt;&lt;/default-interceptor-ref&gt;



3\). paramsPrepareParamsStack 拦截器过程是

params -&gt; modelDriven -&gt; params

所以可以先把请求参数赋给 Action 对应的属性, 再根据赋给 Action 的那个属性值决定压到值栈栈顶的对象, 最后再为栈顶对象的属性赋值.



对于 edit 操作而言:

I.   先为 EmployeeAction 的 employeeId 赋值

II.  根据 employeeId 从数据库中加载对应的对象, 并放入到值栈的栈顶

III. 再为栈顶对象的 employeeId 赋值\(实际上此时 employeeId 属性值已经存在\)

IV.  把栈顶对象的属性回显在表单中.



4\). 关于回显: Struts2 表单标签会从值栈中获取对应的属性值进行回显. 



5\). 存在的问题: 

getModel 方法

```java
public Employee getModel() {
	if(employeeId == null)
		employee = new Employee();
	else
		employee = dao.get(employeeId);
	
	return employee;
}
```

I.   在执行删除的时候, employeeId 不为 null, 但 getModel 方法却从数据库加载了一个对象. 不该加载!

II.  指向查询全部信息时, 也 new Employee\(\) 对象. 浪费!



6\). 解决方案: 使用 PrepareInterceptor 和 Preparable 接口. 

