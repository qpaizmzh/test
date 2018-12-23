# 30.Struts2的文件上传

1. 文件的上传:



1\). 表单需要注意的 3 点



2\). Struts2 的文件上传实际上使用的是 Commons FileUpload 组件, 所以需要导入



commons-fileupload-1.3.jar

commons-io-2.0.1.jar



3\). Struts2 进行文件上传需要使用 FileUpload 拦截器



4\). 基本的文件的上传: 直接在 Action 中定义如下 3 个属性, 并提供对应的 getter 和 setter



//文件对应的 File 对象

private File \[fileFieldName\];

//文件类型

private String \[fileFieldName\]ContentType;

//文件名

private String \[fileFieldName\]FileName;



5\). 使用 IO 流进行文件的上传即可. 



6\). 一次传多个文件怎么办 ?



若传递多个文件, 则上述的 3 个属性, 可以改为 List 类型! 多个文件域的 name 属性值需要一致. 



7\). 可以对上传的文件进行限制吗 ? 例如扩展名, 内容类型, 上传文件的大小 ? 若可以, 则若出错, 显示什么错误消息呢 ? 消息可以定制吗 ? 



可以的!



可以通过配置 FileUploadInterceptor 拦截器的参数的方式来进行限制



maximumSize \(optional\) - 默认的最大值为 2M. 上传的单个文件的最大值



allowedTypes \(optional\) - 允许的上传文件的类型. 多个使用 , 分割



allowedExtensions \(optional\) - 允许的上传文件的扩展名. 多个使用 , 分割.



注意: 在 org.apache.struts2 下的 default.properties 中有对上传的文件总的大小的限制. 可以使用常量的方式来修改该限制



struts.multipart.maxSize=2097152



定制错误消息. 可以在国际化资源文件中定义如下的消息:



struts.messages.error.uploading - 文件上传出错的消息



struts.messages.error.file.too.large - 文件超过最大值的消息



struts.messages.error.content.type.not.allowed - 文件内容类型不合法的消息



struts.messages.error.file.extension.not.allowed - 文件扩展名不合法的消息



问题: 此种方式定制的消息并不完善. 可以参考 org.apache.struts2 下的 struts-messages.properties, 可以提供更多的定制信息.

---

```
<?xml version="1.0" encoding="UTF-8"?>

<!DOCTYPE struts PUBLIC
        "-//Apache Software Foundation//DTD Struts Configuration 2.5//EN"
        "http://struts.apache.org/dtds/struts-2.5.dtd">

<struts>
    <!-- 配置国际化资源文件 -->
    <constant name="struts.custom.i18n.resources" value="i18n"></constant>
    <package name="page" extends="struts-default">
        //修改拦截器的某些属性的写法
        <interceptors>
            <interceptor-stack name="stack">
                <interceptor-ref name="defaultStack">
                    <param name="fileUpload.maximumSize">2097152</param>
                    <param name="fileUpload.allowedTypes">text/html,text/xml</param>
                    <param name="fileUpload.allowedExtensions">html,dtd</param>
                </interceptor-ref>
            </interceptor-stack>
        </interceptors>
        <default-interceptor-ref name="stack"></default-interceptor-ref>
        

        <action name="upload" class="upload.testUpload">
            <result>/upload.jsp</result>
            <result name="input">/upload.jsp</result>

        </action>
    </package>
</struts>
```

```
//这是创建上传文件表单的类：
public class testUpload extends ActionSupport {
//上传单个文件就直接是单类型，如果是多个文件上传就是变为list
    private List<File> ppt;
    private List<String> pptContentType;
    private List<String> pptFileName;

    public List<File> getPpt() {
        return ppt;
    }

    public void setPpt(List<File> ppt) {
        this.ppt = ppt;
    }

    public List<String> getPptContentType() {
        return pptContentType;
    }

    public void setPptContentType(List<String> pptContentType) {
        this.pptContentType = pptContentType;
    }

    public List<String> getPptFileName() {
        return pptFileName;
    }

    public void setPptFileName(List<String> pptFileName) {
        this.pptFileName = pptFileName;
    }

    @Override
    public String execute() throws Exception {
        System.out.println(ppt);
        System.out.println(pptContentType);
        System.out.println(pptFileName);


        //如果是list状态的话也是同样的上传方式，只不过要迭代完成
//        ServletContext context = ServletActionContext.getServletContext();
//        String path = context.getRealPath("/file/" + pptFileName);
//        //String path =  ServletActionContext.getRequest().getSession().getServletContext().getRealPath("/file/" + pptFileName);
//
//        FileInputStream inputStream = new FileInputStream(ppt);
//        FileOutputStream outputStream = new FileOutputStream(path);
//
//        byte[] bytes = new byte[1024];
//        int num = 0;
//        while ((num = inputStream.read(bytes)) != -1) {
//            outputStream.write(bytes, 0, num);
//        }
//
//        outputStream.close();
//        inputStream.close();

        return super.execute();
    }
}
```

通过国际化资源配置文件配置提示信息：

```
//占位符是获取的分别的上传文件的属性（就是定义的上传文件表单类里面的属性）
struts.messages.error.uploading=^Error uploading: {0}
struts.messages.error.file.too.large=^The file is to large to be uploaded: {0} "{1}" "{2}" {3}
struts.messages.error.content.type.not.allowed=^Content-Type not allowed: {0} "{1}" "{2}" {3}
struts.messages.error.file.extension.not.allowed=^File extension not allowed: {0} "{1}" "{2}" {3}
```

upload.jsp

```
<s:form action="testUpload" 
		method="post" enctype="multipart/form-data" theme="simple">
        <s:fielderror name="ppt"></s:fielderror>
		<s:actionerror/>
				
		PPTFile: <s:file name="ppt" label="PPTFile"></s:file>
		//注意的是回显的时候，多文件上传的某些属性是会整个对象进行显示的，所以要加上索引
		//以让它进行正常的显示
		PPTDesc: <s:textfield name="pptDesc[0]" label="PPTDesc"></s:textfield>
	
		<br><br>
		PPTFile:<s:file name="ppt" label="PPTFile"></s:file>
		PPTDesc:<s:textfield name="pptDesc[1]" label="PPTDesc"></s:textfield>

		<br><br>
		PPTFile:<s:file name="ppt" label="PPTFile"></s:file>
		PPTDesc:<s:textfield name="pptDesc[2]" label="PPTDesc"></s:textfield>
		
		<br><br>
		<s:submit></s:submit>
	</s:form>
```



