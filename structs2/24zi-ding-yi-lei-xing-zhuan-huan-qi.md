# 24.自定义类型转换器

![](/assets/24-1.png)

![](/assets/24-2.png)![](/assets/24-3.png)

问题. 如何自定义类型转换器 ?

1\). 为什么需要自定义的类型转换器 ? 因为 Struts 不能自动完成 字符串 到 引用类型 的 转换.

2\). 如何定义类型转换器:

I. 开发类型转换器的类: 扩展 StrutsTypeConverter 类.

II. 配置类型转换器:

有两种方式

①. 基于字段的配置:

* 在字段所在的 Model\(可能是 Action, 可能是一个 JavaBean\) 的包下, 新建一个 ModelClassName-conversion.properties 文件

* 在该文件中输入键值对: fieldName=类型转换器的全类名.

* 在该文件中输入键值对: fieldName=类型转换器的全类名.

* 第一次使用该转换器时创建实例.

  类型转换器是单实例的!

②. 基于类型的配置:

* 在 src 下新建 xwork-conversion.properties

* 键入: 待转换的类型=类型转换器的全类名.

* 在当前 Struts2 应用被加载时创建实例.



