# 通过Aware接口获取web资源

使用applicationAware接口来获取web资源中的application域对象，（像session、Parameter、request等获取的方式是类似的）：

```java
public class TestAwareAction implements ApplicationAware, SessionAware, RequestAware,
    ParameterAware{

    public String execute(){

        //1. 向 application 中加入一个属性: applicationKey2 - applicationValue2
        application.put("applicationKey2", "applicationValue2");

        //2. 从 application 中读取一个属性 date, 并打印. （这个在请求之前的页面就已经创建）
        System.out.println(application.get("date"));

        return "success";
    }

    public String save(){

        return null;
    }

    private Map<String, Object> application;

    @Override
    public void setApplication(Map<String, Object> application) {
        this.application = application;
    }

    @Override
    public void setParameters(Map<String, String[]> parameters) {

    }

    @Override
    public void setRequest(Map<String, Object> request) {
        // TODO Auto-generated method stub

    }

    @Override
    public void setSession(Map<String, Object> session) {
        // TODO Auto-generated method stub

    }

}
```

#### 选用的建议:

若一个 Action 类中有多个 action 方法, 且多个方法都需要使用域对象的 Map 或 parameters, 则建议使用  
Aware 接口的方式。

### Session中注意的事项：

```java
    Map<String, Object> sessionMap = actionContext.getSession();
        sessionMap.put("sessionKey", "sessionValue");

        System.out.println(sessionMap.getClass()); 

        if(sessionMap instanceof SessionMap){
            SessionMap sm = (SessionMap) sessionMap;
            sm.invalidate();
            System.out.println("session 失效了. ");
        }
```

session 对应的 Map 实际上是 SessionMap 类型的! 强转后若调用其 invalidate\(\) 方法, 可以使其 session 失效!

### 使用Servlet API获取web资源

可以实现ServletXX接口来使Struts2注入需要的Servlet的对象，与上面的实现方法类似（这里不再进行讲述）



