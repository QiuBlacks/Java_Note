# 3、SpringMVC跨服务器文件上传

# 一、主要知识

注意：首先要在tomcat/conf/web.xml里配置；使得服务器允许文件写入。

```
<init-param>
  <param-name>readonly</param-name>
  <param-value>false</param-value>
</init-param>
```



# 二、实现步骤

在文件上传基础上，创建个新的tomcat服务器,端口号为9999，如何在此服务器上部署新项目用来传文件

## 1、导入jar包

```
  <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-core</artifactId>
      <version>1.18.1</version>
    </dependency>
    <dependency>
      <groupId>com.sun.jersey</groupId>
      <artifactId>jersey-client</artifactId>
      <version>1.18.1</version>
    </dependency>
```



## 2、配置表单操作页面

```
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Title</title>
</head>
<body>
    <h3>跨服务器文件上传</h3>

    <form action="user/fileupload3" method="post" enctype="multipart/form-data">
        选择文件：<input type="file" name="upload" /><br/>
        <input type="submit" value="上传" />
    </form>

</body>
</html>
```



## 3、文件上传操作

```
@RequestMapping("/user")
public class UserController {
    @RequestMapping("/fileupload3")
    public String fileuoload3(MultipartFile upload) throws Exception {
        System.out.println("跨服务器文件上传...");

        // 定义上传文件服务器路径
        String path = "http://localhost:9999/uploads/";

        // 说明上传文件项
        // 获取上传文件的名称
        String filename = upload.getOriginalFilename();
        // 把文件的名称设置唯一值，uuid
        String uuid = UUID.randomUUID().toString().replace("-", "");
        filename = uuid+"_"+filename;

        // 创建客户端的对象
        Client client = Client.create();
        // 和图片服务器进行连接
        WebResource webResource = client.resource(path + filename);
        // 上传文件
        webResource.put(upload.getBytes());

        return "success";
    }
}
```

