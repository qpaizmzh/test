# spring的配置（配置Bean）

![](/assets/spring-3-1.png)![](/assets/spring-3-2.png)![](/assets/spring-3-4.png)![](/assets/spring-3-5.png)![](/assets/spring-3-6.png)![](/assets/spring-3-8.png)![](/assets/spring-3-8.png)代码：

```
<!-- 配置一个 bean -->
	<bean id="helloWorld" class="com.atguigu.spring.helloworld.HelloWorld">
		<!-- 为属性赋值 -->
		<property name="user" value="Jerry"></property>
	</bean>
	
	<!-- 配置一个 bean -->
	<bean id="helloWorld2" class="com.atguigu.spring.helloworld.HelloWorld">
		<!-- 为属性赋值 -->
		<!-- 通过属性注入: 通过 setter 方法注入属性值 -->
		<property name="user" value="Tom"></property>
	</bean>
	
	<!-- 通过构造器注入属性值 -->
	<bean id="helloWorld3" class="com.atguigu.spring.helloworld.HelloWorld">
		<!-- 要求: 在 Bean 中必须有对应的构造器.  -->
		<constructor-arg value="Mike"></constructor-arg>
	</bean>
	
	<!-- 若一个 bean 有多个构造器, 如何通过构造器来为 bean 的属性赋值 -->
	<!-- 可以根据 index 和 value 进行更加精确的定位. (了解) -->
	<bean id="car" class="com.atguigu.spring.helloworld.Car">
		<constructor-arg value="KUGA" index="1"></constructor-arg>
		<constructor-arg value="ChangAnFord" index="0"></constructor-arg>
		<constructor-arg value="250000" type="float"></constructor-arg>
	</bean>
```



