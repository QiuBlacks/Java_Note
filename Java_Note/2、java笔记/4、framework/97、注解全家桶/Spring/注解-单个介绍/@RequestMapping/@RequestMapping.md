# @RequestMapping

参考[https://blog.csdn.net/J080624/article/details/55193269](https://blog.csdn.net/J080624/article/details/55193269)

## 1、作用

是一个用来处理请求地址映射的注解，可用于类或方法上。用于类上，表示类中的所有响应请求的方法都是以该地址作为父路径

- 类级别：映射请求的 URL
- 方法级别：映射 URL 以及 HTTP 请求方法

## 2、属性

- path(value)：   指定请求的实际地址，指定的地址可以是URI Template 模式（后面将会说明）；
- method：    指定请求的method类型， GET、POST、PUT、DELETE等；
- params：	  指定request中必须包含某些参数值，才能让该方法处理。
- headers： 	  指定request中必须包含某些指定的header值，才能让该方法处理请求。
- consumes：	  指定处理请求的提交内容类型（Content-Type），例如application/json, text/html;
- produces: 	 指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回；

 

##  3、示例

```
@RequestMapping(path="/user")
public class HelloController {

    @RequestMapping(value="/testRequestMapping",
                    method = {RequestMethod.GET},
                    params = {"username=heihei"},
                    headers = {"Accept"})
    public String testRequestMapping(){
        System.out.println("测试RequestMapping注解...");
        return "success";
    }
}
```



 



