# 11.值栈-ValueStack

![](/assets/11-1.png)![](/assets/11-2.png)

![](/assets/11-3.png)



1. 关于值栈:

1\). helloWorld 时, ${productName} 读取 productName 值, 实际上该属性并不在 request 等域对象中, 而是从值栈中获取的. 

2\). ValueStack: 

I.  可以从 ActionContext 中获取值栈对象

II. 值栈分为两个逻辑部分

	&gt; Map 栈: 实际上是 OgnlContext 类型, 是个 Map, 也是对 ActionContext 的一个引用. 里边保存着各种 Map: requestMap, sessionMap, applicationMap, parametersMap, attr

	&gt; 对象栈: 实际上是 CompoundRoot 类型, 是一个使用 ArrayList 定义的栈. 里边保存各种和当前 Action 实例相关的对象.是一个数据结构意义的栈.

