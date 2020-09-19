# @SessionAttributes

# 一、基本介绍

## 1、原理

　　默认情况下Spring MVC将模型中的数据存储到**request域**中。当一个请求结束后，数据就失效了。如果要跨页面使用。那么需要使用到session。而@SessionAttributes注解就可以使得模型中的数据存储一份到session域中。

 

## 2、参数

　　1、names（value）：这是一个字符串数组。里面应写需要存储到session中变量名。

　　2、types：根据指定参数的类型，将模型中对应类型的参数存储到session中



# 二、代码实现

```java
@Controller
@RequestMapping("/anno")
@SessionAttributes(value={"msg"})  
public class AnnoController {
    //存Session
    @RequestMapping(value="/testSessionAttributes")
    public String testSessionAttributes(Model model){
        System.out.println("testSessionAttributes...");
        // 底层会存储到request域对象中
        model.addAttribute("msg","美美");
        return "success";
    }

   //获取Session
    @RequestMapping(value="/getSessionAttributes")
    public String getSessionAttributes(ModelMap modelMap){
        System.out.println("getSessionAttributes...");
        String msg = (String) modelMap.get("msg");
        System.out.println(msg);
        return "success";
    }

  //删除Session
    @RequestMapping(value="/delSessionAttributes")
    public String delSessionAttributes(SessionStatus status){
        System.out.println("getSessionAttributes...");
        status.setComplete();
        return "success";
    }
```



## 测试

在首页jsp设置： 

```
   <a href="anno/testSessionAttributes">testSessionAttributes</a>
    <a href="anno/getSessionAttributes">getSessionAttributes</a>
    <a href="anno/delSessionAttributes">delSessionAttributes</a>
```

在输出页面设置：

isELIgnored="false" 开启正则表达式

```
<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="UTF-8" isELIgnored="false" %>
<html>
<head>
    <title>Title</title>
</head>
<body>

<h3>入门成功success</h3>

${requestScope}
${sessionScope}
${sessionScope.msg}

</body>
</html>
```

