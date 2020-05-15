# **@RequestParam**

## **一、基本介绍**

### **1、作用**

**是传递参数的，用于将请求参数区数据映射到功能处理方法的参数上（解决请求的参数和方法中的参数名字不一致的问题）**

### **2、属性**

- **value（name)**：参数名字，即入参的请求参数名字，如username表示请求的参数区中的名字为username的参数的值将传入；
- **required**：是否必须，默认是true，表示请求中一定要有相应的参数，否则将报404错误码；
- **defaultValue**：默认值，表示如果请求中没有同名参数时的默认值，默认值可以是SpEL表达式，如“#{systemProperties['java.vm.version']}”。

# **二、实例**

**方法中的value即为jsp文件传来的name值，然后赋给方法的参数username**

```
@Controller
@RequestMapping("/anno")
public class AnnoController {
    @RequestMapping("/testRequestParam")
    public String testRequestParam(@RequestParam(value="name") String username){
        System.out.println("执行了...");
        System.out.println(username);
        return "success";
    }
}
```

```
<body>
    <form action="anno/testRequestParam" method="post">
        用户姓名：<input type="text" name="name" /><br/>
        <input type="submit" value="提交" />
    </form>
</body>
```

