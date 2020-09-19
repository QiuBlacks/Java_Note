

# Thymeleaf模板引擎

## 一、基础知识

### 1、基本介绍

thymeleaf 跟 JSP 一样，就是运行之后，就得到纯 HTML了。 区别在与，不运行之前， thymeleaf 也是 纯 html ...
		所以 thymeleaf 不需要 服务端的支持，就能够被以 html 的方式打开，这样就方便前端人员独立设计与调试, jsp 就不行了， 不启动服务器 jsp 都没法运行出结果来。

### 2、标准表达式语法

变量表达式 				：	${session.user.name}

选择或星号表达式 	：    *{...}

文字国际化表达式	：#{...}

URL 表达式   				：@{...}

片段引用表达式			：~ {...}



常用的命名空间

```
xmlns:th="http://www.thymeleaf.org"
xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
xmlns:shiro="http://www.pollix.at/thymeleaf/shiro"

<html lang="en" xmlns:th="http://www.thymeleaf.org"
xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
```









## 二、源码分析

```

@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
    private Charset encoding;
}
```

我们可以在其中看到默认的前缀和后缀！

我们只需要把我们的**html页面**放在类路径下的**templates**下，thymeleaf就可以帮我们自动渲染了。

使用thymeleaf什么都不需要配置，只需要将他放在指定的文件夹下即可！





## 三、环境搭建

### 1、导入pom依赖

Thymeleaf 官网：https://www.thymeleaf.org/

Thymeleaf 在Github 的主页：https://github.com/thymeleaf/thymeleaf

Spring官方文档：找到我们对应的版本

https://docs.spring.io/spring-boot/docs/2.2.5.RELEASE/reference/htmlsingle/#using-boot-starter 

```
  <!--thymeleaf-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-thymeleaf</artifactId>
  </dependency>
```

或者

```
  <!--thymeleaf-->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring5</artifactId>
        </dependency>
        <dependency>
            <groupId>org.thymeleaf.extras</groupId>
            <artifactId>thymeleaf-extras-java8time</artifactId>
        </dependency>
```



### 2、编写html页面

导入命名空间的约束

```
 xmlns:th="http://www.thymeleaf.org"
```

编写页面

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <!--/*@thymesVar id="msg" type=""*/-->
        <div th:text="${msg}"></div>
    </body>
</html>
```

### 3、编写Controller类

```
@Controller
public class helloController {
    @RequestMapping("/hello")
    public String hello(Model model){
        model.addAttribute("msg","hello wo");
        return "hello";
    }
}
```





## 参考文章

[狂神说SpringBoot11：Thymeleaf模板引擎](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&mid=2247483807&idx=1&sn=7e1d5df51cdeb046eb37dec7701af47b&scene=19#wechat_redirect)













