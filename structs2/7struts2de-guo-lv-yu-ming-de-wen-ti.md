## 关于 Struts2 请求的扩展名问题



1\). org.apache.struts2 包下的 default.properties 中配置了 Struts2 应用个的一些常量



2\). struts.action.extension 定义了当前 Struts2 应用可以接受的请求的扩展名.



3\). 可以在 struts.xml 文件中以常量配置的方式修改 default.properties 所配置的常量.



&lt;constant name="struts.action.extension" value="action,do,"&gt;&lt;/constant&gt;

