action VS Action 类

1\). action: 代表一个  Struts2 的请求.

2\). Action 类: 能够处理 Struts2 请求的类.

&gt; 属性的名字必须遵守与 JavaBeans 属性名相同的命名规则.

属性的类型可以是任意类型. 从字符串到非字符串\\\(基本数据库类型\\\)之间的数据转换可以自动发生

&gt;必须有一个不带参的构造器: 通过反射创建实例

&gt; 至少有一个供 struts 在执行这个 action 时调用的方法

&gt;同一个 Action 类可以包含多个 action 方法.

&gt; Struts2 会为每一个 HTTP 请求创建一个新的 Action 实例, 即 Action 不是单例的, 是线程安全的.

