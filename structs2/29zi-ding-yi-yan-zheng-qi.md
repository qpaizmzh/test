# 29.自定义验证器

自定义验证器:



I.   定义一个验证器的类

	&gt; 自定义的验证器都需要实现 Validator. 

	&gt; 可以选择继承 ValidatorSupport 或 FieldValidatorSupport 类

	&gt; 若希望实现一个一般的验证器, 则可以继承 ValidatorSupport

	&gt; 若希望实现一个字段验证器, 则可以继承 FieldValidatorSupport

	&gt; 具体实现可以参考目前已经有的验证器. 

	&gt; 若验证程序需要接受一个输入参数, 需要为这个参数增加一个相应的属性



II.  在配置文件中配置验证器

	&gt; 默认情况下下, Struts2 会在 类路径的根目录下加载 validators.xml 文件. 在该文件中加载验证器.

该文件的定义方式同默认的验证器的那个配置文件: 位于 com.opensymphony.xwork2.validator.validators 下的 default.xml



       &gt; 若类路径下没有指定的验证器, 则从 com.opensymphony.xwork2.validator.validators 下的 default.xml 中的验证器加载     



III. 使用: 和目前的验证器一样. 



IV. 示例代码: 自定义一个 18 位身份证验证器       

