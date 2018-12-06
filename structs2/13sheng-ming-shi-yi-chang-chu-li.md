# 13.声明式异常处理

![](/assets/13-1.png)

```java
<package name="default" extends="struts-default">
        <global-results>
            <result name="input">index.jsp</result>
        </global-results>
        //创建的声名异常的name属性要和某个result的name属性一致
        <global-exception-mappings>
            <exception-mapping exception="java.lang.RuntimeException" result="input"></exception-mapping>
        </global-exception-mappings>
        
        
        <action name="product" class="ognl2.Product" method="success">
            <result name="success">
                /success.jsp
            </result>
        </action>
    </package>
```



