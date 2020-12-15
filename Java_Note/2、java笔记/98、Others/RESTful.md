# RESTful架构

# 一、基础知识

Representational State Transfer：表现层状态转换

## 1、基本介绍

RESTful不是一种标准，而是一种设计风格。

### 1.1 什么是RESTful架构：

- 每一个URI代表一种资源；
- 客户端和服务器之间，传递这种资源的某种表现层；
- 客户端通过HTTP动词(GET,POST等等)，对服务器端资源进行操作，实现"表现层状态转化"。

### 1.2 概念

- **Resource**（资源） ：对象的单个实例。例如，一只动物。它可以是一段文本、一张图片、一首歌曲、一种服务，总之就是一个具体的实在。你可以用一个**URI（统一资源标识符）**指向它，每种资源对应一个特定的URI。要获取这个资源，访问它的URI就可以，因此URI就成了每一个资源的地址或独一无二的识别符。
- **集合**：对象的集合。例如，动物。
- **第三方**：使用我们接口的开发者
- **表现层（Representation）**："资源"是一种信息实体，它可以有多种外在表现形式。我们把"资源"具体呈现出来的形式，叫做它的"表现层"（Representation）。
- **状态转化（State Transfer）** 访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。



## 2、主要作用

充分并正确利用HTTP协议的特性，规范资源获取的URL路径。通俗地讲，RESTful风格的设计允许将参数通过URL拼接传到服务端，目的是让URL看起来更简洁实用。并且对于不同操作，要指定不同的HTTP方法（POST/GET/PUT/DETELE）



# 二、REST接口规范

## 1、动作

- GET （SELECT）：从服务器检索特定资源，或资源列表。
- POST （CREATE）：在服务器上创建一个新的资源。
- PUT （UPDATE）：更新服务器上的资源（客户端提供更新后的整个资源）。
- PATCH （UPDATE）：更新服务器上的资源（客户端提供更改的属性，可以看做作是部分更新）。
- DELETE （DELETE）：从服务器删除资源。



## 2、路径（接口命名）

接口尽量使用名词，禁止使用动词，下面是一些例子。

```
 /emp/1  HTTP GET    查询id=1的emp
 /emp/1  HTTP DELETE   删除id=1的emp,实验中直接删除会报405错误，但是采用$.ajax异步删除就没问题
/emp/1  HTTP PUT   跟新emp
 /emp/add  HTTP POST   新增emp
```

## 3、版本（Versioning）

应该将API的版本号放入URL。如：

```
https://api.example.com/v1/复制代码
```

另一种做法是，将版本号放在HTTP头信息中，但不如放入URL方便和直观。Github采用这种做法。



#### 4、过滤信息（Filtering）

#### 5、状态码（Status Codes）





# 二、SpringMVC实现RESTful风格

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