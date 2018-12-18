# 27.Struts2的运行过程分析

## ![](/assets/27-1.png)自己复述的版本：

1. 浏览器发送request请求，首先经过StrutsPrepareAndExecuteFilter的接收，把请求丢到ActionMapper，使用了去掉关键字的方法之后看域名是否是Struts2的请求，如果不是，直接调用dochain的方法直接跳过Struts2的执行，把请求直接交换给原生的Filter，让它执行后续。
2. 如果是Struts2的请求，Struts2会创建一个ActionProxy，用于调用指定的Action,而ActionProxy指定的参数又是从ConfigurationManager中获取了Struts.xml文件里面的属性中来的。
3. ActionProxy在struts框架中发挥着非常重要的作用。通过webwork和xwork交互关系图可以看出，它是action和xwork中间的一层。 正因为ActionProxy的存在导致Action调用更加简洁。ActionProxy的主要作用是连接Struts和xwork，方便通信操作。

4. ActionProxy会创建一个ActionInvocation实例，然后通过该实例不停地调用拦截器，知道调用到了Action，操作完该Action、并将Result结果书写完成之后，然后再继续执行后面的过滤器，最终将结果返回到response中。

### PPT复述的版本：

1. 请求发送给 StrutsPrepareAndExecuteFilter

2. StrutsPrepareAndExecuteFilter 询问 ActionMapper： 该请求是否是一个 Struts2 请求（即是否返回一个非空的 ActionMapping 对象）

3. 若 ActionMapper 认为该请求是一个 Struts2 请求，则 StrutsPrepareAndExecuteFilter 把请求的处理交给 ActionProxy

4. ActionProxy 通过 Configuration Manager 询问框架的配置文件，确定需要调用的 Action 类及 Action 方法

5. ActionProxy 创建一个 ActionInvocation 的实例，并进行初始化

6. ActionInvocation 实例在调用Action的过程前后，涉及到相关拦截器（Intercepter）的调用。

7. Action 执行完毕，ActionInvocation 负责根据 struts.xml 中的配置找到对应的返回结果。调用结果的 execute 方法，渲染结果。在渲染的过程中可以使用Struts2 框架中的标签。

8. 执行各个拦截器 invocation.invoke\(\) 之后的代码

9. 把结果发送到客户端





