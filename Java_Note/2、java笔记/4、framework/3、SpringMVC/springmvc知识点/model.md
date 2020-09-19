# model

## 一、基本介绍

### 1、概念

控制器是通过Model对象把数据传到view层的

### 2、作用

主要是保存数据，可以借助它们将数据带到前端

### 3、使用方法

```java
  @RequestMapping("/testString")
    public String testString(Model model){
        System.out.println("testString方法执行了...");
        // 模拟从数据库中查询出User对象
        User user = new User();
        user.setUsername("美美");
        user.setPassword("123");
        user.setAge(30);
        // model对象
        model.addAttribute("user",user);
        return "success";
    }
```

model会将数据存放在requestScope中，然后我们可以在表现层调用

```
${user.Username}
${requestScope}
```



## 二、ModelMap

### 1、继承LinkedHashMap

```
public class ModelMap extends LinkedHashMap<String,Object>
```



### 2、作用

主要用于传递控制方法处理数据到结果页面，也就是说我们把结果页面上需要的数据放到ModelMap对象中即可，他的作用类似于request对象的setAttribute方法的作用，用来在一个请求过程中传递处理的数据

通过以下方法向页面传递参数：

```
addAttribute(String key,Object value);
```

modelmap本身**不能设置页面跳转的url地址别名或者物理跳转地址**，那么我们可以通过控制器方法的**字符串返回值**来设置跳转url地址别名或者物理跳转地址。



### 3、使用

需要返回的是**地址路径的字符串**

```
@RequestMapping("hello")
public String testVelocity(ModelMap model,String name){
    model.addAttribute("name",name);
    return "hello";
}
```

然后在jsp页面上可以通过el表达式语言$attributeName等系列数据展示标签获取并展示modelmap中的数据。 
		





## 三、ModelAndView





### 2、作用

2.1  设置转向地址（ModelAndView和ModelMap的主要区别）

```
ModelAndView view = new ModelAndView("path:ok");
```

2.2	将控制器方法中处理的结果数据传递到结果页面，也就是把在结果页面上需要的数据放到ModelAndView对象中即可，其作用类似于request对象的setAttribute方法的作用，用来在一个请求过程中传递处理的数据。通过以下方法向页面传递参数：

```
public ModelAndView addObject(String attributeName, Object attributeValue){...}
public ModelAndView addObject(Object attributeValue){...}
```



### 3、实例

需要返回对象

```java
public ModelAndView xxxxmethod(String someparam)
{
     //省略方法处理逻辑若干
      //构建ModelAndView实例，并设置跳转地址
      ModelAndView view = new ModelAndView("path:handleok");
      //将数据放置到ModelAndView对象view中,第二个参数可以是任何java类型
      view.addObject("key",someparam);
     ......
     //返回ModelAndView对象view
      return view;
}
```







