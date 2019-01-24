# 使用BEAN工厂方法创建BEAN实例

![](/assets/spring-11-1.png)![](/assets/spring-11-2.png)静态工厂类的例子编写：

```
//静态工厂的方法
public class StaticFactory {

    private static Map<String, Car> maps = new HashMap<>();

    static {
        maps.put("audi", new Car());
        maps.put("ford", new Car());
    }

    public static Car getCar(String name) {
        return maps.get(name);
    }

}
```

实例工厂方法的写法：

```
//实例工厂的写法
public class InstanceFactory {

    private static Map<String, Car> maps;

    public InstanceFactory() {
        maps = new HashMap<>();
        maps.put("audi", new Car());
        maps.put("ford", new Car());
    }

    public  Car getCar(String name) {
        return maps.get(name);
    }

}
```

spring-bean.xml中的关键bean配置（里面的类名和上面的没有关系，只是案例，不用纠结）

```
	<!-- 通过工厂方法的方式来配置 bean -->
	<!-- 1. 通过静态工厂方法: 一个类中有一个静态方法, 可以返回一个类的实例(了解) -->
	<!-- 在 class 中指定静态工厂方法的全类名, 在 factory-method 中指定静态工厂方法的方法名 -->
	<bean id="dateFormat" class="java.text.DateFormat" factory-method="getDateInstance">
		<!-- 可以通过 constructor-arg 子节点为静态工厂方法指定参数 -->
		<constructor-arg value="2"></constructor-arg>
	</bean>
	
	<!-- 2. 实例工厂方法: 先需要创建工厂对象, 再调用工厂的非静态方法返回实例(了解) -->
	<!-- ①. 创建工厂对应的 bean -->
	<bean id="simpleDateFormat" class="java.text.SimpleDateFormat">
		<constructor-arg value="yyyy-MM-dd hh:mm:ss"></constructor-arg>
	</bean>
	
	<!-- ②. 有实例工厂方法来创建 bean 实例 -->
	<!-- factory-bean 指向工厂 bean, factory-method 指定工厂方法(了解) -->
	<bean id="datetime" factory-bean="simpleDateFormat" factory-method="parse">
		<!-- 通过 constructor-arg 执行调用工厂方法需要传入的参数 -->
		<constructor-arg value="1990-12-12 12:12:12"></constructor-arg>
	</bean>
	
	<!-- 配置通过 FactroyBean 的方式来创建 bean 的实例(了解) -->
	<bean id="user" class="com.atguigu.spring.ref.UserBean"></bean>
```



