# 管理BEAN的生命周期

![](/assets/spring-10-1.png)

![](/assets/spring-10-2.png)![](/assets/spring-10-3.png)

code:

```
tips:    1、创建的BEAN类需要添加初始化方法和销毁方法，探后就在配置文件的BEAN标签中添加init-method和
            destroy-method中添加各自的方法
         2、创建BEAN后置处理器的话，需要添加一个实现该接口的类，然后在配置文件添加该标签
    
    
    
    <!--当使用 init-method 和 destroy-method 的时候，
    使用 prototype 时 Spring 不会负责销毁容器对象，即 Spring 不会调用 destroy-method 所指定的方法，
    所以需要去掉 scope 属性，使用默认的 singleton-->
    <bean id="person" class="com.spring.beans.autowire.Person"
          autowire="byName" init-method="init" destroy-method="destroy"></bean>

    <!--配置BEAN的后置处理器，首先是创建了BeanPostProcessor接口的类，然后在xml配置文件
    写上配置-->
    <bean class="com.spring.beans.autowire.BeanProcessor"></bean>
```



