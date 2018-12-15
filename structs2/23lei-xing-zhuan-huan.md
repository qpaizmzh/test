# 23.类型转换

* ![](/assets/23-1.png)

![](/assets/23-2.png)![](/assets/23-4.png)

问题1: 如何覆盖默认的错误消息?

1\). 在对应的 Action 类所在的包中新建

 ActionClassName.properties 文件, ActionClassName 即为包含着输入字段的 Action 类的类名

2\). 在属性文件中添加如下键值对: invalid.fieldvalue.fieldName=xxx

问题2: 如果是 simple 主题, 还会自动显示错误消息吗? 如果不会显示, 怎么办 ?

1\). 通过 debug 标签, 可知若转换出错, 则在值栈的 Action\(实现了 ValidationAware 接口\) 对象中有一个 fieldErrors 属性.

该属性的类型为 

`Map<String, List<String>>`

键: 字段\(属性名\), 值: 错误消息组成的 List. 所以可以使用 LE 或 OGNL 的方式

来显示错误消息: ${fieldErrors.age\[0\]}



2\). 还可以使用 s:fielderror 标签来显示. 可以通过 fieldName 属性显示指定字段的错误.

问题3. 若是 simple 主题, 且使用 

`<s:fielderror fieldName="age">`

`</s:fielderror>`

 来显示错误消息, 则该消息在一个ul, li, span 中. 如何去除 ul, li, span 呢 ?

在 template.simple 下面的 fielderror.ftl 定义了 simple 主题下, s:fielderror 标签显示错误消息的样式. 所以修改该

配置文件即可. 在 src 下新建 template.simple 包, 新建 fielderror.ftl 文件, 把原生的 fielderror.ftl 中的内容

复制到新建的 fielderror.ftl 中, 然后剔除 ul, li, span 部分即可.



问题4. 如何自定义类型转换器 ?

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

                                                                                                    

###                                                                源代码

---

```
	<s:form action="testConversion" theme="simple">
		Age: <s:textfield name="age" label="Age"></s:textfield>
		${fieldErrors.age[0] }
		^<s:fielderror fieldName="age"></s:fielderror>
		<br><br>
		
		Birth: <s:textfield name="birth"></s:textfield>
		<s:fielderror fieldName="birth"></s:fielderror>
		<br><br>
		
		<s:submit></s:submit>
	</s:form>
```



