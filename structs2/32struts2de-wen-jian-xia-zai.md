# 32.Struts2的文件下载

![](/assets/32-1.png)2. 文件的下载:

1\). Struts2 中使用 type="stream" 的 result 进行下载即可

2\). 具体使用细节参看 struts-2.3.15.3-all/struts-2.3.15.3/docs/WW/docs/stream-result.html

3\). 可以为 stream 的 result 设定如下参数

contentType: 结果类型

contentLength: 下载的文件的长度

contentDisposition: 设定 Content-Dispositoin 响应头. 该响应头指定接应是一个文件下载类型, 一般取值为  attachment;filename="document.pdf".

inputName: 指定文件输入流的 getter 定义的那个属性的名字. 默认为 inputStream

bufferSize: 缓存的大小. 默认为 1024

allowCaching: 是否允许使用缓存 

contentCharSet: 指定下载的字符集 

4\). 以上参数可以在 Action 中以 getter 方法的方式提供!

##                                         源代码

---

```
//struts.xml
<struts>
    <!-- 配置国际化资源文件 -->
    <constant name="struts.custom.i18n.resources" value="i18n"></constant>
    <package name="page" extends="struts-default">
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
        <action name="test" class="validation.testAction">
            <result name="success">/success.jsp</result>
            <result name="input">/index.jsp</result>
        </action>

        <action name="upload" class="upload.testUpload">
            <result>/upload.jsp</result>
            <result name="input">/upload.jsp</result>

        </action>
        //下载的主要配置方式是在这里
        <action name="download" class="download.downloadTest">
            <result name="success" type="stream" >
                <param name="bufferSize">2048</param>//设置的传送缓冲区的大小
                //设置的内容
                <param name="contentDisposition">attachment;filename ="hidden.html"</param>
            </result>
        </action>
    </package>
</struts>
```

//要实现的download类：

```
package download;

import com.opensymphony.xwork2.ActionSupport;
import org.apache.struts2.ServletActionContext;

import javax.servlet.ServletContext;
import java.io.FileInputStream;
import java.io.InputStream;

public class downloadTest extends ActionSupport {
//可以直接在类中实现这几个属性，也可以在struts.xml中设置这几个属性
    private String contentType;
    private long contentLength;
    private String contentDisposition;
    private InputStream inputStream;

    public String getContentType() {
        return contentType;
    }

    public void setContentType(String contentType) {
        this.contentType = contentType;
    }

    public long getContentLength() {
        return contentLength;
    }

    public void setContentLength(long contentLength) {
        this.contentLength = contentLength;
    }

    public String getContentDisposition() {
        return contentDisposition;
    }

    public InputStream getInputStream() {
        return inputStream;
    }

    @Override
    public String execute() throws Exception {
        contentType = "text/html";
        contentDisposition = "attachment;filename ='hidden.html'";

        ServletContext servletContext = ServletActionContext.getServletContext();
        String filename =  servletContext.getRealPath("/file/hidden.html");
        inputStream = new FileInputStream(filename);
        contentLength = inputStream.available();
        return super.execute();
    }
}

```

超链接实现相关文件的下载：

```
<%@ taglib prefix="s" uri="/struts-tags" %>

<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>$Title$</title>
  </head>
  <body>
  <s:debug></s:debug>
  <a href="download">download</a>//主要的下载链接是这个
  </body>
</html>

```



