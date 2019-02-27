# spring-struts2的整合

1\). 整合目标 ? 使 IOC 容器来管理 Struts2 的 Action!

2\). 如何进行整合 ? 

①. 正常加入 Struts2

②. 在 Spring 的 IOC 容器中配置 Struts2 的 Action

注意: 在 IOC 容器中配置 Struts2 的 Action 时, 需要配置 scope 属性, 其值必须为 prototype

```
<bean id="personAction" 
	class="com.atguigu.spring.struts2.actions.PersonAction"
	scope="prototype">
	<property name="personService" ref="personService"></property>	
</bean>
```



③. 配置 Struts2 的配置文件: action 节点的 class 属性需要指向 IOC 容器中该 bean 的 id

```
<action name="person-save" class="personAction">
	<result>/success.jsp</result>
</action> 
```



④. 加入 struts2-spring-plugin-2.3.15.3.jar



3\). 整合原理: 通过添加 struts2-spring-plugin-2.3.15.3.jar 以后, Struts2 会先从 IOC 容器中

获取 Action 的实例.

```
if (appContext.containsBean(beanName)) {
    o = appContext.getBean(beanName);
} else {
    Class beanClazz = getClassInstance(beanName);
    o = buildBean(beanClazz, extraContext);
}
```





代码实例：

创建一个可以Action类

```
package com.spring.action;

import com.spring.Services.PersonService;

public class personAction {
    private PersonService personService;

    public void setPersonService(PersonService personService) {
        this.personService = personService;
    }

    public String execute() {
        System.out.println("execute...");
        return "success";
    }
}

```

在application.xml的文件中配置Action类和其他相关类

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="person" class="com.spring.bean.Person">
        <property name="name" value="123"></property>
        <property name="num" value="21"></property>
    </bean>

    <bean id="personService" class="com.spring.Services.PersonService"></bean>

    <!--因为Struts每一次创建Action都是新的实例，所以要在scope上写成prototype，允许创建多实例-->
    <bean id="personAction" class="com.spring.action.personAction" scope="prototype">
        <property name="personService" ref="personService"></property>
    </bean>


</beans>
```

Struts.xml中的配置在IDEA中会显示错误，但是实际上是能运行项目的，目前并不清楚原因：

```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.3//EN"
        "http://struts.apache.org/dtds/struts-2.3.dtd">

<struts>
    <constant name="struts.devMode" value="true"></constant>
    <constant name="struts.enable.DynamicMethodInvocation" value="false"></constant>
    <package name="default" namespace="/" extends="struts-default">
        <action name="person-save" class="personAction">
            <result>/success.jsp</result>
        </action>
    </package>
</struts>
```

web.xml中的配置：

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <!--这是自行创建监听器的方法-->
    <!--<listener>-->
    <!--<listener-class>com.spring.Listener.SpringContextListener</listener-class>-->
    <!--</listener>-->
    <servlet>
        <servlet-name>dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>*.form</url-pattern>
    </servlet-mapping>

    <!--配置Struts2的Filter-->
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter</filter-class>
    </filter>

    <filter-mapping>
        <filter-name>struts2</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```



