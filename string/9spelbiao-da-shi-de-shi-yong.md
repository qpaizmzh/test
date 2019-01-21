# SPEL表达式的使用

![](/assets/spring-9-1.png)![](/assets/spring-9-2.png)![](/assets/spring-9-3.png)![](/assets/spring-9-4.png)![](/assets/spring-9-5.png)![](/assets/spring-9-6.png)

```

    <!-- 测试 SpEL: 可以为属性进行动态的赋值(了解) -->
    <bean id="girl" class="com.atguigu.spring.helloworld.User">
        <property name="userName" value="周迅"></property>
        <!--注意一下下面的这两个的用法-->
        <property name="abc" value="#{T(java.lang.Math).PI*80}"></property>
        <property name="cdr" value="#{car.price>30000?'ha':'pe'}"></property>
    </bean>

    <bean id="boy" class="com.atguigu.spring.helloworld.User" init-method="init" destroy-method="destroy">
        <property name="userName" value="高胜远"></property>
        <property name="wifeName" value="#{girl.userName}"></property>
    </bean>
```



