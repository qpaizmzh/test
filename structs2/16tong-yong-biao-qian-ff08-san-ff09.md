# Sort标签



### example：

创建student类：

```
public class Student {
    private String name;
    private int age;
        super();
        this.name=name;
        this.age=age;
    }

    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }

}
```

### 实现Comparator

```
public class Cmp implements Comparator<Student>{
    public int compare(Student stu1, Student stu2) {
        //根据年龄排序
        return  stu1.getAge()-stu2.getAge();
    }
}
```

### 用Action创建List:

```
public class SortTagAction extends ActionSupport{

    private List<Student> students = new ArrayList<Student>();
    public String execute(){
        Student stu1=new Student("张灿",20);
        Student stu2=new Student("刘志杰",19);
        Student stu3=new Student("彭涛",21);
        Student stu4=new Student("鲁荣明",18);

        students.add(stu1);
        students.add(stu2);
        students.add(stu3);
        students.add(stu4);

        return SUCCESS;
    }
    public List<Student> getStudents() {
        return students;
    }
    public void setStudents(List<Student> students) {
        this.students = students;
    }
}
```

### 使用sort标签进行排序：

```
<%@ page language="java" import="java.util.*" pageEncoding="utf-8"%>
<%@ taglib uri="/struts-tags" prefix="s" %>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <title>sort.jsp</title>
  </head>
  <body>
    <s:bean name="org.cmp.Cmp" id="cmp"/>
    <s:sort comparator="cmp" source="students" id="sort">
            <s:iterator>  
                    <s:property value="name"/>  
                    <s:property value="age"/>
                    <br>
            </s:iterator> 
    </s:sort>
  </body>
</html>
```

### 修改struts.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE struts PUBLIC "-//Apache Software Foundation//DTD Struts Configuration 2.1//EN" "http://struts.apache.org/dtds/struts-2.1.dtd">
<struts>
    <constant name="struts.devMode" value="true" />
    <package name="default" namespace="/" extends="struts-default">
        <action name="sortTagAction" class="org.action.SortTagAction" >
            <result name="success">sort.jsp</result>
        </action>
    </package>

</struts>    
```



