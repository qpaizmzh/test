# spring在web应用中的使用（为整合Struts2做准备）

spring在web应用上没有什么大的配置方式的改变，但是还是有几个注意点：

* 注意添加spring中web和webmvc的jar包
* 注意application.xml的路径选择问题，出现了找不到该配置文件的异常，直接复制到SRC目录下



大概步骤就是：

1、在web应用中加载服务器的时候就应该通过Spring中的监听器将IOC容器进行加载，所以先创建对应的监听器

2、application.xml的基本配置不需要变化

3、在web.xml中添加context-param，配置好application.xml的路径信息，等待监听器捕获并在监听器中的初始方法创建IOC容器实例

4、然后就进行调用



有另外一种方法就是通过spring自带的内置webapplicationUtils直接调用application.xml，从而创建IOC容器，web.xml的application.xml的路径信息同样需要配置，但是监听器换成一个spring内置的org.springframework.web.context.ContextLoaderListener，然后就可以进行调用

主要代码：

web.xml:

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
    //这是spring内置的监听器
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
</web-app>
```

SpringContextListener\(使用内置webApplicationUtils的不需要这个类创建\):

```
package com.spring.Listener;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import javax.servlet.ServletContext;
import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.http.HttpSessionAttributeListener;
import javax.servlet.http.HttpSessionEvent;
import javax.servlet.http.HttpSessionListener;
import javax.servlet.http.HttpSessionBindingEvent;

public class SpringContextListener implements ServletContextListener,
        HttpSessionListener, HttpSessionAttributeListener {

    // Public constructor is required by servlet spec
    public SpringContextListener() {
    }

    // -------------------------------------------------------
    // ServletContextListener implementation
    // -------------------------------------------------------
    public void contextInitialized(ServletContextEvent sce) {
      /* This method is called when the servlet context is
         initialized(when the Web application is deployed). 
         You can initialize servlet context related data here.
      */

        //因为可以直接从ServletContext的事件中可以监听的到，我们可以直接从该事件中获取：
        ServletContext servletContext = sce.getServletContext();
        String config = servletContext.getInitParameter("contextConfigLocation");


        //1、创建IOC容器(根据上面的路径创建IOC容器)
        ApplicationContext context = new ClassPathXmlApplicationContext(config);
        //2、在Application属性中添加该IOC容器键值对，然后可以供其他地方进行使用
        servletContext.setAttribute("context", context);

    }

    public void contextDestroyed(ServletContextEvent sce) {
      /* This method is invoked when the Servlet Context 
         (the Web application) is undeployed or 
         Application Server shuts down.
      */
    }

    // -------------------------------------------------------
    // HttpSessionListener implementation
    // -------------------------------------------------------
    public void sessionCreated(HttpSessionEvent se) {
        /* Session is created. */
    }

    public void sessionDestroyed(HttpSessionEvent se) {
        /* Session is destroyed. */
    }

    // -------------------------------------------------------
    // HttpSessionAttributeListener implementation
    // -------------------------------------------------------

    public void attributeAdded(HttpSessionBindingEvent sbe) {
      /* This method is called when an attribute 
         is added to a session.
      */
    }

    public void attributeRemoved(HttpSessionBindingEvent sbe) {
      /* This method is called when an attribute
         is removed from a session.
      */
    }

    public void attributeReplaced(HttpSessionBindingEvent sbe) {
      /* This method is invoked when an attibute
         is replaced in a session.
      */
    }
}

```

创建一个Servlet，方便网页进行访问调用：

```
package com.spring.Servlet;

import com.spring.bean.Person;
import org.springframework.context.ApplicationContext;

import javax.servlet.ServletContext;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@WebServlet(name = "TestServlet",urlPatterns = "/TestServlet")
public class TestServlet extends HttpServlet {
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletContext servletContext = getServletContext();
        ApplicationContext applicationContext = (ApplicationContext) servletContext.getAttribute("context");
        //自行创建的person类，只是演示使用，不是主要代码
        Person person = applicationContext.getBean(Person.class);
        response.getWriter().print(person.getName()+person.getNum());

    }
}

```

index.jsp:

```
<%@ page import="org.springframework.context.ApplicationContext" %>
<%@ page import="org.springframework.web.context.support.WebApplicationContextUtils" %>
<%@ page import="com.spring.bean.Person" %><%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 2019/2/25
  Time: 21:03
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>$Title$</title>
</head>
<body>
//对应上面的Servlet类的创建，演示使用，不是核心代码
<a href="TestServlet">123</a>
$END$
//内置方法的演示
<%
    ApplicationContext ctx = null;
    //内置方法主要使用到WebApplicationContextUtils这个工具类获得IOC实例
    ctx = WebApplicationContextUtils.getWebApplicationContext(application);
    Person person = ctx.getBean(Person.class);
    System.out.println(person.getName());
    System.out.println( person.getNum());
%>
</body>
</html>

```



