# @Controller

@Controller 注解是专门用于处理 Http 请求处理的，是以 MVC 为核心的设计思想的控制层。

使用@Controller 注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面

### 应用场景

@Controller 一般应用在有返回界面的应用场景下.

例如，管理后台使用了 thymeleaf 作为模板开发，需要从后台直接返回 Model 对象到前台，那么这时候就需要使用 @Controller 来注解。





# @RestController

@RestController 则是 @Controller 的子注解。

@RestController == @Controller + @ResponseBody 

**但使用@ResponseBody 这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面**



### 应用场景

@RestController 如果只是接口，那么就用 RestController 来注解.

例如前端页面全部使用了 Html、Jquery来开发，通过 Ajax 请求服务端接口，那么接口就使用 @RestController 统一注解。