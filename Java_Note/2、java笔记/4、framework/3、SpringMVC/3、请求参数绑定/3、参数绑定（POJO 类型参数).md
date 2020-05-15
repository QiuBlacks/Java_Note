# **参数绑定（POJO 类型参数)**

## **在基本类型参数步骤基础上**

### **1、Account 类添加User实体**

```
  public class Account implements Serializable{
    private String username;
    private String password;
    private Double money;
    
    private User user;
}  
```



## **2、编辑param.jap**

**注意：  param/saveAccount  不能写成：/param/saveAccount**

**accout调用实体类user的属性 user.name**

**name的名称必须和实体类的属性名一样**

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<form action="param/saveAccount" method="post">
    姓名：<input type="text" name="username" /><br/>
    密码：<input type="text" name="password" /><br/>
    金额：<input type="text" name="money" /><br/>
    用户姓名：<input type="text" name="user.uname" /><br/>
    用户年龄：<input type="text" name="user.age" /><br/>
    <input type="submit" value="提交" />
</form>

</body>
</html>
```



## **3、测试**

```
@Controller
@RequestMapping("/param")
public class ParamController {
  
    @RequestMapping("/saveAccount")
    public String saveAccount(Account account){
        System.out.println("执行了...");
        System.out.println(account);
        return "success";
    }
}
```

