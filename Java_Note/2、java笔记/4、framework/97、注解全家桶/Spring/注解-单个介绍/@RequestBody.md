# @RequestBody

# 一、基本介绍

## 1、作用：

注解实现接收http请求的json数据，将json转换为java对象。 

   i) 该注解用于读取Request请求的body部分数据，使用系统默认配置的HttpMessageConverter进行解析，然后把相应的数据绑定到要返回的对象上；

   ii) 再把HttpMessageConverter返回的对象数据绑定到 controller中方法的参数上。

|          |               |                        |
| -------- | ------------- | ---------------------- |
| 请求参数 | @RequestBody  | 请求参数以json格式提交 |
| 返回参数 | @ResponseBody | 返回 json 格式         |

## 2、使用时机：

A)   GET、POST方式提交时， 根据request header Content-Type的值来判断:

-   application/x-www-form-urlencoded， 可选（即非必须，因为这种情况的数据@RequestParam, @ModelAttribute也可以处理，当然@RequestBody也能处理）；
-   multipart/form-data, 不能处理（即使用@RequestBody不能处理这种格式的数据）；
-   其他格式， 必须（其他格式包括application/json, application/xml等。这些格式的数据，必须使用@RequestBody来处理）；

B)   PUT方式提交时， 根据request header Content-Type的值来判断:

-   application/x-www-form-urlencoded， 必须；
-   multipart/form-data, 不能处理；
-   其他格式， 必须；

说明：request的body部分的数据编码格式由header部分的Content-Type指定；



# 二、案例

```
<form action="anno/testRequestBody" method="post">
        用户姓名：<input type="text" name="username" /><br/>
        用户年龄：<input type="text" name="age" /><br/>
        <input type="submit" value="提交" />
    </form>
```



```
@RequestMapping("/testRequestBody")
    public String testRequestBody(@RequestBody String body){
        System.out.println("执行了...");
        //对body进行转码
        String s=URLDecoder.decode(body,"utf-8");
        System.out.println(s);
        return "success";
    }
```

将表中的所有数据作为一个请求体输出来：：

```
执行了... 
name=张三&age=12
```





# @ResponseBody

作用： 该注解用于将Controller的方法返回的对象，通过适当的HttpMessageConverter转换为指定格式后，写入到Response对象的body数据区。

使用时机：返回的数据不是html标签的页面，而是其他某种格式的数据时（如json、xml等）使用；