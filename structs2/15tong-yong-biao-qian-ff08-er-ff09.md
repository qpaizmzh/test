# 15.通用标签（二）

## Set标签

![](/assets/15-1.png)

### example:

  action：

```
package com.rhwayfun.struts2.action;

import com.rhwayfun.struts2.bean.User;

public class SetTagAction {

    private User user;
    public String execute(){
        user = new User();
        user.setName("刘备");
        return "success";
    }

    public User getUser() {
        return user;
    }
}

```

jsp：

```
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s" %>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    <title>set标签测试</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    
  </head>

  <body>
    <p>action范围中获取值</p>
    <p>
        <s:set name="name" value="user.name"></s:set>
        <s:property value="#name"/>
    </p>
    <p>request范围中获取</p>
    <p>
        <s:property value="#request.name"/>
    </p>
    <p>session范围中获取</p>
    <p>
        <s:set name="name" value="user.name" scope="session"></s:set>
        <s:property value="#session.name"/>
    </p>
  </body>
</html>
```

# **push标签**

![](/assets/15-2.png)

正常在jsp页面使用Struts 2标签库进行访问的方式是\#request.user.name和

\#request.user.age

（假设是request范围），而使用push标签，访问name属性的方式是：

```
<s:push value="#request.user">
    姓名：<s:property value="name"/>
    年龄：<s:property value="age"/>
</s:push>
```

# **iterator标签**

![](/assets/15-3.png)

![](/assets/15-4.png)

```
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%@ taglib uri="/struts-tags" prefix="s" %>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    <title>iterator标签测试</title>
    <meta http-equiv="pragma" content="no-cache">
    <meta http-equiv="cache-control" content="no-cache">
    <meta http-equiv="expires" content="0">    

  </head>

  <body>
    <s:iterator value="{'刘备','张飞','关羽'}" status="s">
        <p>当前元素：<s:property /></p>
        <p>当前元素总数：<s:property value="#s.count"/></p>
        <p>当前元素索引：<s:property value="#s.index"/></p>
        <p>当前元素是否为偶数：<s:property value="#s.even"/></p>
        <p>当前元素是否是奇数：<s:property value="#s.odd"/></p>
        <p>当前元素是否是第一个数：<s:property value="#s.first"/></p>
        <p>当前元素是否是最后一个数：<s:property value="#s.last"/></p>
        <hr>
    </s:iterator>
  </body>
</html>
```



