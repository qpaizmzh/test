# 25.类型转换与复杂属性配合使用

## ![](/assets/25-1.png)![](/assets/25-2.png)示例代码：

创建一个实体类：

```
package listAction;

import java.util.Date;

public class Member {
    private String memberName;
    private Date memberDate;

    @Override
    public String toString() {
        return "Member{" +
                "memberName='" + memberName + '\'' +
                ", memberDate=" + memberDate +
                '}';
    }

    public String getMemberName() {
        return memberName;
    }

    public void setMemberName(String memberName) {
        this.memberName = memberName;
    }

    public Date getMemberDate() {
        return memberDate;
    }

    public void setMemberDate(Date memberDate) {
        this.memberDate = memberDate;
    }
}

```

在Action中添加对应的引用属性：

```
package listAction;

import com.opensymphony.xwork2.ActionSupport;
import com.opensymphony.xwork2.ModelDriven;
import org.apache.struts2.interceptor.RequestAware;

import java.util.Date;
import java.util.Map;
import java.util.Random;

public class userAction extends ActionSupport implements RequestAware, ModelDriven<user> {

    private Member member;

    public Member getMember() {
        return member;
    }

    public void setMember(Member member) {
        this.member = member;
    }

}

```

在JSP页面上添加属性：

```
 <s:form action="convert">
    <s:textfield name="date" label="Date"></s:textfield>
    <s:textfield name="member.memberName" label="memberName"></s:textfield>
    <s:textfield name="member.memberDate" label="memberDate"></s:textfield>

            <s:submit></s:submit>
  </s:form>
```



