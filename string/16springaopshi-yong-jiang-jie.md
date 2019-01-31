# springAOP使用讲解

![](/assets/spring-16-1.png)![](/assets/spring-16-2.png)

 AOP 的 helloWorld

 1. 加入 jar 包

 com.springsource.net.sf.cglib-2.2.0.jar

com.springsource.org.aopalliance-1.0.0.jar

 com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar

spring-aspects-4.0.0.RELEASE.jar



 2. 在 Spring 的配置文件中加入 aop 的命名空间。



 3. 基于注解的方式来使用 AOP

 3.1 在配置文件中配置自动扫描的包: 

&lt;context:component-scan base-package="com.atguigu.spring.aop"&gt;&lt;/context:component-scan&gt;

 

3.2 加入使 AspjectJ 注解起作用的配置: 

&lt;aop:aspectj-autoproxy&gt;&lt;/aop:aspectj-autoproxy&gt;

为匹配的类自动生成动态代理对象.



4. 编写切面类:

 4.1 一个一般的 Java 类

 4.2 在其中添加要额外实现的功能.



 5. 配置切面

5.1 切面必须是 IOC 中的 bean: 实际添加了 @Component 注解

5.2 声明是一个切面: 添加 @Aspect

5.3 声明通知: 即额外加入功能对应的方法.

5.3.1 前置通知: @Before\("execution\(public int com.atguigu.spring.aop.ArithmeticCalculator.\*\(int, int\)\)"\)

@Before 表示在目标方法执行之前执行 @Before 标记的方法的方法体.

@Before 里面的是切入点表达式:



6. 在通知中访问连接细节: 可以在通知方法中添加 JoinPoint 类型的参数, 从中可以访问到方法的签名和方法的参数.



 7. @After 表示后置通知: 在方法执行之后执行的代码.





![](/assets/spring-16-3.png)



代码：

```
 
```



