# @Controller和@RestController



@Controller 注解是专门用于处理 Http 请求处理的，是以 MVC 为核心的设计思想的控制层。

使用@Controller 注解，在对应的方法上，视图解析器可以解析return 的jsp,html页面，并且跳转到相应页面





@RestController 则是 @Controller 的子注解。

@RestController == @Controller + @ResponseBody 

**但使用@ResponseBody 这个注解，就不能返回jsp,html页面，视图解析器无法解析jsp,html页面**