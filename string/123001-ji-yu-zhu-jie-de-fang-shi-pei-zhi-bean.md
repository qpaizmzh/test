# 基于注解的方式配置Bean

![](/assets/spring-12-1.png)![](/assets/spirng-12-2.png)![](/assets/spring-12-3.png)code:

spring的Bean配置文件：

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation= "http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
<!--
result-pattern:这个功能可以根据表达式只扫描该package下的类
context:include-filter:这个标签和result-pattern同时使用，有先使用result-pattern
                        这个标签只会包含表达式的所在的类，但是如果use-default-filters为true（默认）,会忽略该过滤器
                        所以要实现这个，要将use-default-filters改成false
context:exclude-filter:这个标签和result-pattern同时使用，有先使用result-pattern
                        这个标签根据表达式的类来排除符合条件的bean,这个不受use-default-filters影响
-->
    <context:component-scan base-package="com.spring.annotation">
        <!--<context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"></context:include-filter>-->

        <!--assignable表述的是根据表达式的类来筛选符合条件的子类和本类-->
        <context:exclude-filter type="assignable" expression="com.spring.annotation.repository.userRepository"></context:exclude-filter>
    </context:component-scan>

</beans>
```

main:

```
  //这些类都自行添加，并把对应的注解添加上
  //repository的注解这样写的话：@repository（“userRepository”）的话，getBean（）方法的参数就要是这个
   注解里面的参数的名字
  public static void main(String[] args) {
        ApplicationContext cxt = new ClassPathXmlApplicationContext("com/spring/annotation/annotation.xml");
       testObject te2 = (testObject) cxt.getBean("testObject");
        System.out.println(te2);

        UserService us = (UserService) cxt.getBean("userService");
        System.out.println(us);
        userRepository ur = (userRepository) cxt.getBean("userRepository");
        System.out.println(ur);
        testController tc = (testController) cxt.getBean("testController");
        System.out.println(tc);

    }
```



