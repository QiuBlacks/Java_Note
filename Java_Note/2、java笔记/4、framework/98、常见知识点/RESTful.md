# 一、RESTful（表现层状态转换）

## **1、基本介绍**

RESTful不是一种标准，而是一种设计风格。

## **2、主要作用**

充分并正确利用HTTP协议的特性，规范资源获取的URL路径。通俗地讲，RESTful风格的设计允许将**参数通**过URL拼接传到服务端，目的是让URL看起来更简洁实用。并且对于不同操作，要指定不同的HTTP方法（POST/GET/PUT/DETELE）

## **3、举例**

 /emp/1  HTTP GET    查询id=1的emp

​    /emp/1  HTTP DELETE   删除id=1的emp,实验中直接删除会报405错误，但是采用$.ajax异步删除就没问题

​    /emp/1  HTTP PUT   跟新emp

​    /emp/add  HTTP POST   新增emp



# 二、SpringMVC实现RESTful风格**

![image-20200513144207279](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181602.png)

## **1、** **Spring对RESTful的支持**

**利用@RequestMapping 指定要处理请求的URI模板和HTTP请求的动作类型**

**- 利用@PathVariable讲URI请求模板中的变量映射到处理方法参数上**

**- 利用Ajax,在客户端发出PUT、DELETE动作的请求**



## **2、示例**

```java
   @RequestMapping(value="/testPathVariable/{sid}")
    public String testPathVariable(@PathVariable(name="sid") String id){
        System.out.println("执行了...");
        System.out.println(id);
        return "success";
    }
```

**此时，jsp文件的请求路径应该这么写：**

```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<body>
    <a href="anno/testPathVariable/10">testPathVariable</a>
</body>
```



参考：https://github.com/Snailclimb/JavaGuide/blob/master/docs/system-design/restful-api.md