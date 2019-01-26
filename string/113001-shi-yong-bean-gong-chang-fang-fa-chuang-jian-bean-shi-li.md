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
    <!-- ①、通过静态工厂方法的方式来配置 bean -->
    <!--
        class属性：指向静态工厂方法的全类名
        factory-method:指向静态工厂方法的名字
        constructor-args:如果工厂方法要传入参数，则使用constructor-args来配置参数
    -->
    <bean id="factory" class="com.spring.beans.autowire.StaticFactory" factory-method="getCar">
    <constructor-arg value="audi"></constructor-arg>
    </bean>

    <!-- ②. 有实例工厂方法来创建 bean 实例 -->
    <!-- factory-bean 指向工厂 bean, factory-method 指定工厂方法(了解) -->
     <!--配置工厂的实例-->
    <bean id="instanceFactory" class="com.spring.beans.autowire.InstanceFactory"></bean>
    <!--通过实例工厂方法配置Bean-->
    <bean id="car2" factory-bean="instanceFactory" factory-method="getCar">
        <constructor-arg value="audi"></constructor-arg>
    </bean>
```



