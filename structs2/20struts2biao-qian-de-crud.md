# 20.Struts2标签的CRUD

## Struts2 运行流程图-1

![](/assets/20-1.png)

## Params拦截器

* Parameters 拦截器将把表单字段映射到 ValueStack 栈的栈顶对象的各个属性中. 如果某个字段在模型里没有匹配的属性, Param 拦截器将尝试 ValueStack 栈中的下一个对象

* ![](/assets/20-2.png)

## ModelDriven 拦截器

* 当用户触发 add 请求时, ModelDriven 拦截器将调用 EmployeeAction 对象的getModel\(\) 方法, 并把返回的模型\(Employee实例\)压入到 ValueStack 栈.

* 接下来 Parameters 拦截器将把表单字段映射到 ValueStack 栈的栈顶对象的各个属性中. 因为此时 ValueStack 栈的栈顶元素是刚被压入的模型\(Employee\)对象, 所以该模型将被填充. 如果某个字段在模型里没有匹配的属性, Param 拦截器将尝试 ValueStack 栈中的下一个对象

* ![](/assets/20-3.png)

* ![](/assets/20-4.png)

* ![](/assets/20-5.png)

* ![](/assets/20-6.png)

## 简单

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

```java
public Employee getModel() {
	return new Employee();
}    
```





