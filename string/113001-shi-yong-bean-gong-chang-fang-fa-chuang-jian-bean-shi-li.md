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

![](/assets/spring-11-5.png)

code:

* 创建实现了factoryBean接口的bean类：

```
package com.spring.beans.factoryBean;

import com.spring.beans.autowire.Car;
import org.springframework.beans.factory.FactoryBean;

public class CarFactoryBean implements FactoryBean<Car> {

    private String brand;

    public void setBrand(String brand) {
        this.brand = brand;
    }

    public String getBrand() {
        return brand;
    }

    @Override
    public Car getObject() throws Exception {

        return new Car(brand,50000);
    }

    @Override
    public Class<?> getObjectType() {
        return Car.class;
    }

    @Override
    public boolean isSingleton() {
        return false;
    }
}
```

* 在spring配置的xml文件中添加对应的bean:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--
    通过FactoryBean来配置Bean的实例
    class:指向FactoryBean的全类名
    property：配置FactoryBean的属性

    调用这个方法的时候是调用了FactoryBean中的getObject()方法返回实例
    -->
    <bean id="carFactoryBean" class="com.spring.beans.factoryBean.CarFactoryBean">
        <property name="brand" value="BMW"></property>
    </bean>

</beans>
```



