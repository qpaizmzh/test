# AOP基本概念和原理理解

![](/assets/spring-15-1.png)

![](/assets/spring-15-2.png)![](/assets/spring-15-3.png)![](/assets/spring-15-4.png)

代码示例：

```
//先模拟动态代理的例子
//动态代理用的主要是类加载器ClassLoader,Class数组（存的一般是接口或者是父类的class属性），还有的是InvocationHandler




//主要编写的代理类
package com.atguigu.spring.aop;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.Arrays;

public class ArithmeticCalculatorLoggingProxy {
	
	//要代理的对象（这个自己创建可以，一个接口，一个实现类）
	private ArithmeticCalculator target;
	
	public ArithmeticCalculatorLoggingProxy(ArithmeticCalculator target) {
		super();
		this.target = target;
	}

	//返回代理对象
	public ArithmeticCalculator getLoggingProxy(){
		ArithmeticCalculator proxy = null;
		
		ClassLoader loader = target.getClass().getClassLoader();
		Class [] interfaces = new Class[]{ArithmeticCalculator.class};
		//注意InvocationHandler的定义不能在调用代理类的主类里，
		InvocationHandler h = new InvocationHandler() {
			/**
			 * proxy: 代理对象。 一般不使用该对象
			 * method: 正在被调用的方法
			 * args: 调用方法传入的参数
			 */
			@Override
			public Object invoke(Object proxy, Method method, Object[] args)
					throws Throwable {
				String methodName = method.getName();
				//打印日志
				System.out.println("[before] The method " + methodName + " begins with " + Arrays.asList(args));
				
				//调用目标方法
				Object result = null;
				
				try {
					//前置通知
					
//注意InvocationHandler的调用不能在即将调用目标对象的类里面进行定义(也就是不可以使用这个主调用类的属性作为目标对象的注入)，
//如果强行这样的话,InvocationHandler会将主调用类变成代理类，实际上这个方法会将invoke方法里面的代理对象不断调用，
//也就是说主调用类调用目标对象的时候触发了InvocationHandler里面的实现方法，然后里面的method.invoke方法调用的刚好是主调用类的属性，
//这样也就再一次创建了主调用类的代理类，里面就再一次调用了该方法，如此往复（只针对PSVM方法来讲）
				
					result = method.invoke(target, args);
					//返回通知, 可以访问到方法的返回值
				} catch (NullPointerException e) {
					e.printStackTrace();
					//异常通知, 可以访问到方法出现的异常
				}
				
				//后置通知. 因为方法可以能会出异常, 所以访问不到方法的返回值
				
				//打印日志
				System.out.println("[after] The method ends with " + result);
				
				return result;
			}
		};
		
		/**
		 * loader: 代理对象使用的类加载器。 
		 * interfaces: 指定代理对象的类型. 即代理代理对象中可以有哪些方法. 
		 * h: 当具体调用代理对象的方法时, 应该如何进行响应, 实际上就是调用 InvocationHandler 的 invoke 方法
		 */
		proxy = (ArithmeticCalculator) Proxy.newProxyInstance(loader, interfaces, h);
		
		return proxy;
	}
}




//主调用类
package com.atguigu.spring.aop;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Main {
	
	public static void main(String[] args) {
		ArithmeticCalculator arithmeticCalculator = new ArithmeticCalculatorImpl();
		
		arithmeticCalculator = 
				new ArithmeticCalculatorLoggingProxy(arithmeticCalculator).getLoggingProxy();
		
		int result = arithmeticCalculator.add(11, 12);
		System.out.println("result:" + result);
		
		result = arithmeticCalculator.div(21, 3);
		System.out.println("result:" + result);

	}
	
}


```



