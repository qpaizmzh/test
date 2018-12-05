# 12.OGNL使用要点

* 在JSP页面上可以可以利用OGNL\(Object-Graph Navigation Language:对象-图导航语言\)访问到值栈\(ValueStack\)里的对象属性.

* 若希望访问值栈中的ContextMap中的数据，需要给OGNL表达式上添加一个前缀字符‘\#’，如果没有前缀字符‘\#’，搜索将在ObjectStack进行

* Struts的property标签用来输出值栈中的一个属性值：

```java
//这里的[0]相当于是访问从栈顶访问第一个符合productName名字的元素
<s:property value="[0].productName"/>
```



