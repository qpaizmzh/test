# 29.自定义验证器

自定义验证器:

I.   定义一个验证器的类

&gt;自定义的验证器都需要实现 Validator.

&gt;可以选择继承 ValidatorSupport 或 FieldValidatorSupport 类

&gt;若希望实现一个一般的验证器, 则可以继承 ValidatorSupport

&gt;若希望实现一个字段验证器, 则可以继承 FieldValidatorSupport

&gt;具体实现可以参考目前已经有的验证器.

&gt;若验证程序需要接受一个输入参数, 需要为这个参数增加一个相应的属性

II.  在配置文件中配置验证器

&gt;默认情况下下, Struts2 会在 类路径的根目录下加载 validators.xml 文件. 在该文件中加载验证器.

该文件的定义方式同默认的验证器的那个配置文件: 位于 com.opensymphony.xwork2.validator.validators 下的 default.xml

&gt;若类路径下没有指定的验证器, 则从 com.opensymphony.xwork2.validator.validators 下的 default.xml 中的验证器加载

III. 使用: 和目前的验证器一样.

IV. 示例代码: 自定义一个 18 位身份证验证器

---

//创建一个新的validator的类

```
package validation;

import com.opensymphony.xwork2.validator.ValidationException;
import com.opensymphony.xwork2.validator.validators.FieldValidatorSupport;

public class IDValidator extends FieldValidatorSupport {
    @Override
    public void validate(Object o) throws ValidationException {
        String filedName = getFieldName();
        Object value = this.getFieldValue(filedName,o);


        IDCard id = new IDCard();
        boolean bool = id.Verify((String) value);

        if(!bool){
            addFieldError(filedName,o);
        }

    }
}

```

//自己弄一个类似的做验证的方法类：id

```
package validation;

public class IDCard {
	final int[] wi = { 7, 9, 10, 5, 8, 4, 2, 1, 6, 3, 7, 9, 10, 5, 8, 4, 2, 1 };
	final int[] vi = { 1, 0, 'X', 9, 8, 7, 6, 5, 4, 3, 2 };
	private int[] ai = new int[18];

	public IDCard() {}

	public boolean Verify(String idcard) {
		if (idcard.length() == 15) {
			idcard = uptoeighteen(idcard);
		}
		if (idcard.length() != 18) {
			return false;
		}
		String verify = idcard.substring(17, 18);
		if (verify.equals(getVerify(idcard))) {
			return true;
		}
		return false;
	}

	public String getVerify(String eightcardid) {
		int remaining = 0;

		if (eightcardid.length() == 18) {
			eightcardid = eightcardid.substring(0, 17);
		}

		if (eightcardid.length() == 17) {
			int sum = 0;
			for (int i = 0; i < 17; i++) {
				String k = eightcardid.substring(i, i + 1);
				ai[i] = Integer.parseInt(k);
			}

			for (int i = 0; i < 17; i++) {
				sum = sum + wi[i] * ai[i];
			}
			remaining = sum % 11;
		}

		return remaining == 2 ? "X" : String.valueOf(vi[remaining]);
	}

	public String uptoeighteen(String fifteencardid) {
		String eightcardid = fifteencardid.substring(0, 6);
		eightcardid = eightcardid + "19";
		eightcardid = eightcardid + fifteencardid.substring(6, 15);
		eightcardid = eightcardid + getVerify(eightcardid);
		return eightcardid;
	}
	
	public static void main(String[] args) {
		
		String idcard1 = "350211197607142059"; 
		String idcard2 = "350211197607442059";
		
		IDCard idcard = new IDCard(); 
		System.out.println(idcard.Verify(idcard1)); 
		System.out.println(idcard.Verify(idcard2)); 
	}

}

```

//在src定义类的根目录下面添加validators.xml

```
<?xml version="1.0" encoding="UTF-8"?>

//注意这个文档的放的是什么地址，必须是validator Definition的，如果是其他的是无效的，模板可参照上面的
//笔记中写有的文件路径寻找
<!DOCTYPE validators PUBLIC
        "-//Apache Struts//XWork Validator Definition 1.0//EN"
        "http://struts.apache.org/dtds/xwork-validator-definition-1.0.dtd">

<!-- START SNIPPET: validators-default -->
<validators>
    <validator name="idcard" class="validation.IDValidator"></validator>
</validators>
        <!--  END SNIPPET: validators-default -->
```



