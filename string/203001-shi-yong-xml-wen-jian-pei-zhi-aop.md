# 使用XML文件配置AOP

![](/assets/spring-20-1.png)![](/assets/spring-20-2.png)

![](/assets/spring-20-3.png)

![](/assets/spring-20-4.png)![](/assets/spring-20-5.png)

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.0.xsd">

	<!-- 配置 bean -->
	<bean id="arithmeticCalculator" 
		class="com.atguigu.spring.aop.xml.ArithmeticCalculatorImpl"></bean>

	<!-- 配置切面的 bean. -->
	<bean id="loggingAspect"
		class="com.atguigu.spring.aop.xml.LoggingAspect"></bean>

	<bean id="vlidationAspect"
		class="com.atguigu.spring.aop.xml.VlidationAspect"></bean>

	<!-- 配置 AOP -->
	<aop:config>
		<!-- 配置切点表达式 -->
		<aop:pointcut expression="execution(* com.atguigu.spring.aop.xml.ArithmeticCalculator.*(int, int))" 
			id="pointcut"/>
		<!-- 配置切面及通知 -->
		<aop:aspect ref="loggingAspect" order="2">
			<aop:before method="beforeMethod" pointcut-ref="pointcut"/>
			<aop:after method="afterMethod" pointcut-ref="pointcut"/>
			<aop:after-throwing method="afterThrowing" pointcut-ref="pointcut" throwing="e"/>
			<aop:after-returning method="afterReturning" pointcut-ref="pointcut" returning="result"/>
			<!--  
			<aop:around method="aroundMethod" pointcut-ref="pointcut"/>
			-->
		</aop:aspect>	
		<aop:aspect ref="vlidationAspect" order="1">
			<aop:before method="validateArgs" pointcut-ref="pointcut"/>
		</aop:aspect>
	</aop:config>

</beans>

```


