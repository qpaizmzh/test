# 20.Struts2标签的CRUD

1. Action 实现 ModelDriven 接口后的运行流程

1\). 先会执行 ModelDrivenInterceptor 的 intercept 方法. 

```java
	public String intercept(ActionInvocation invocation) throws Exception {

		// 获取 Action 对象: EmployeeAction 对象, 此时该 Action 已经实现了 ModelDriven 接口

		// public class EmployeeAction implements RequestAware, ModelDriven<Employee>

		Object action = invocation.getAction();

		// 判断 action 是否是 ModelDriven 的实例

		if (action instanceof ModelDriven) {

			// 强制转换为 ModelDriven 类型

			ModelDriven modelDriven = (ModelDriven) action;

			// 获取值栈

			ValueStack stack = invocation.getStack();

			// 调用 ModelDriven 接口的 getModel() 方法

			// 即调用 EmployeeAction 的 getModel() 方法

			/*
			 * 
			 * public Employee getModel() {
			 * 
			 * employee = new Employee();
			 * 
			 * return employee;
			 * 
			 * }
			 * 
			 */

			Object model = modelDriven.getModel();

			if (model != null) {

				// 把 getModel() 方法的返回值压入到值栈的栈顶. 实际压入的是 EmployeeAction 的 employee 成员变量

				stack.push(model);

			}

			if (refreshModelBeforeResult) {

				invocation.addPreResultListener(new RefreshModelBeforeResult(modelDriven, model));

			}

		}

		return invocation.invoke();

	}

}

```

2\). 执行 ParametersInterceptor 的 intercept 方法: 把请求参数的值赋给栈顶对象对应的属性. 若栈顶对象没有对应的属性, 则查询

值栈中下一个对象对应的属性...



3\). 注意: getModel 方法不能提供以下实现. 的确会返回一个 Employee 对象到值栈的栈顶. 但当前 Action 

的 employee 成员变量却是 null. 



public Employee getModel\(\) {

	return new Employee\(\);

}    
