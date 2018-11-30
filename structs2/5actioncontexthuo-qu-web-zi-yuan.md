# 5、ActionContext获取web资源

0、获取ActionContext对象

      ActionContext是Action的上下文对象，可以从中获取到当往Action需要的一切信息

```
ActionContext actionContext = ActionContext.getContext();
```

1、获取application 对应的 Map,并向其中添加一个属性

用过调用ActionContext对象的getApplication\(\)方法来获取application对象的Map对象

```
Map<String, Object> applicationMap = actionContext.getApplication();
```

设置属性：

`applicationMap.put("applicationKey", "applicationValue");`

```
//获取属性
Object date = applicationMap.get("date");
System.out.println("date: " + date); 
```

2、session

```
Map<String, Object> sessionMap = actionContext.getSession();
sessionMap.put("sessionKey", "sessionValue");
		
System.out.println(sessionMap.getClass()); 
		
if(sessionMap instanceof SessionMap){
	SessionMap sm = (SessionMap) sessionMap;
	sm.invalidate();
	System.out.println("session 失效了. ");
		}
```

3、request

```
//ActionContext 中并没有提供 getRequest 方法来获取 request 对应的 Map
//需要手工调用 get() 方法, 传入 request 字符串来获取. 
Map<String, Object> requestMap = (Map<String, Object>) actionContext.get("request");
requestMap.put("requestKey", "requestValue");
```

4、获取请求参数对应的Map，并获取指定的参数值

键：请求参数的名字，值：请求参数的值对应的字符串数组

```
//注意: 1. getParameters 的返回值为在 Map<String, Object>, 而不是 Map<String, String[]>
//     2. parameters 这个 Map 只能读, 不能写入数据, 如果写入, 但不出错, 但也不起作用!
Map<String, Object> parameters = actionContext.getParameters();
System.out.println(((String[])parameters.get("name"))[0]);

//这句设置之后再JSP页面上并不能获取到对应的属性，说明是只能读不能写的				
parameters.put("age", 100);
		
return "success";
```



