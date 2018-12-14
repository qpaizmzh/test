# 22.Preparable拦截器

* Struts2.0的modelDriven拦截器负责把Action类以外的对象压入到值栈的栈顶，而prepare拦截器负责准备为getModel\(\)方法准备对应的对象
* ![](/assets/22-1.png)![](/assets/22-2.png)

![](/assets/22-3.png)



7\). 关于 PrepareInterceptor

\[分析后得到的结论\]

若 Action 实现了 Preparable 接口, 则 Struts 将尝试执行 prepare\[ActionMethodName\] 方法,

若 prepare\[ActionMethodName\] 不存在, 则将尝试执行 prepareDo\[ActionMethodName\] 方法.

若都不存在, 就都不执行.



若 PrepareInterceptor  的 alwaysInvokePrepare 属性为 false, 

则 Struts2 将不会调用实现了 Preparable 接口的  Action 的 prepare\(\) 方法



\[能解决 5\) 的问题的方案\]



可以为每一个 ActionMethod 准备 prepare\[ActionMethdName\] 方法, 而抛弃掉原来的 prepare\(\) 方法

将 PrepareInterceptor  的 alwaysInvokePrepare 属性置为 false, 以避免 Struts2 框架再调用 prepare\(\) 方法.



如何在配置文件中为拦截器栈的属性赋值: 参看 /struts-2.3.15.3/docs/WW/docs/interceptors.html



```
<interceptors>
    <interceptor-stack name="parentStack">
        <interceptor-ref name="defaultStack">
            <param name="params.excludeParams">token</param>
        </interceptor-ref>
    </interceptor-stack>
</interceptors>
 
<default-interceptor-ref name="parentStack"/>
```



----------------------------------源代码解析---------------------------------

```java
public String doIntercept(ActionInvocation invocation) throws Exception {
	//获取 Action 实例
    Object action = invocation.getAction();

	//判断 Action 是否实现了 Preparable 接口
    if (action instanceof Preparable) {
        try {
            String[] prefixes;
            //根据当前拦截器的 firstCallPrepareDo(默认为 false) 属性确定 prefixes
            if (firstCallPrepareDo) {
                prefixes = new String[] {ALT_PREPARE_PREFIX, PREPARE_PREFIX};
            } else {
                prefixes = new String[] {PREPARE_PREFIX, ALT_PREPARE_PREFIX};
            }
            //若为 false, 则 prefixes: prepare, prepareDo
            //调用前缀方法.
            PrefixMethodInvocationUtil.invokePrefixMethod(invocation, prefixes);
        }
        catch (InvocationTargetException e) {

            Throwable cause = e.getCause();
            if (cause instanceof Exception) {
                throw (Exception) cause;
            } else if(cause instanceof Error) {
                throw (Error) cause;
            } else {
                throw e;
            }
        }

		//根据当前拦截器的 alwaysInvokePrepare(默认是 true) 决定是否调用 Action 的 prepare 方法
        if (alwaysInvokePrepare) {
            ((Preparable) action).prepare();
        }
    }

    return invocation.invoke();
}

PrefixMethodInvocationUtil.invokePrefixMethod(invocation, prefixes) 方法: 

public static void invokePrefixMethod(ActionInvocation actionInvocation, String[] prefixes) throws InvocationTargetException, IllegalAccessException {
	//获取 Action 实例
	Object action = actionInvocation.getAction();
	//获取要调用的 Action 方法的名字(update)
	String methodName = actionInvocation.getProxy().getMethod();
	
	if (methodName == null) {
		// if null returns (possible according to the docs), use the default execute 
        methodName = DEFAULT_INVOCATION_METHODNAME;
	}
	
	//获取前缀方法
	Method method = getPrefixedMethod(prefixes, methodName, action);
	
	//若方法不为 null, 则通过反射调用前缀方法
	if (method != null) {
		method.invoke(action, new Object[0]);
	}
}

PrefixMethodInvocationUtil.getPrefixedMethod 方法: 

public static Method getPrefixedMethod(String[] prefixes, String methodName, Object action) {
	assert(prefixes != null);
	//把方法的首字母变为大写
	String capitalizedMethodName = capitalizeMethodName(methodName);
    
    //遍历前缀数组
    for (String prefixe : prefixes) {
        //通过拼接的方式, 得到前缀方法名: 第一次 prepareUpdate, 第二次 prepareDoUpdate
        String prefixedMethodName = prefixe + capitalizedMethodName;
        try {
        	//利用反射获从 action 中获取对应的方法, 若有直接返回. 并结束循环.
            return action.getClass().getMethod(prefixedMethodName, EMPTY_CLASS_ARRAY);
        }
        catch (NoSuchMethodException e) {
            // hmm -- OK, try next prefix
            if (LOG.isDebugEnabled()) {
                LOG.debug("cannot find method [#0] in action [#1]", prefixedMethodName, action.toString());
            }
        }
    }
	return null;
}
```



