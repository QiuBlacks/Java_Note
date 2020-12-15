# SpringMVC

# 一、MVC  模型

MVC 全名是 Model View Controller，是模型(model)－视图(view)－控制器(controller)的缩写，是一种用于设计创建 Web 应用程序表现层的模式。MVC 中每个部分各司其职：

### 1、Model （模型） ：  javabean

通常指的就是我们的数据模型。作用一般情况下用于封装数据。

### 2、View （视图） ： jsp

通常指的就是我们的 jsp 或者 html。作用一般就是展示数据的

通常视图是依据模型数据创建的。

### 3、Controller （控制器） ： servlet

是应用程序中处理用户交互的部分。作用一般就是处理程序逻辑的。

它相对于前两个不是很好理解，这里举个例子：

我们要保存一个用户的信息，该用户信息中包含了姓名，性别，年龄等等。

这时候表单输入要求年龄必须是 1~100 之间的整数。姓名和性别不能为空。并且把数据填充到模型之中。

此时除了 js 的校验之外，服务器端也应该有数据准确性的校验，那么校验就是控制器的该做的。

当校验失败后，由控制器负责把错误页面展示给使用者。

如果校验成功，也是控制器负责把数据填充到模型，并且调用业务层实现完整的业务需求。



## 4、好处

分层设计，实现了业务系统各个组件之间的解耦，有利于业务系统的可扩展性，可维护性。

有利于系统的并行开发，提升开发效率。





# 二、SpringMVC位置（控制层，表现层）



<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170624.png" alt="image-20200513141408292" style="zoom:200%;" />



# 三、SpringMVC 的优势

1、清晰的角色划分：

​		前端控制器（DispatcherServlet）

​		请求到处理器映射（HandlerMapping）

​		处理器适配器（HandlerAdapter）

​		视图解析器（ViewResolver）

​		处理器或页面控制器（Controller）

​		验证器（ Validator）

​		命令对象（Command 请求参数绑定到的对象就叫命令对象）

​		表单对象（Form Object 提供给表单展示和提交到的对象就叫表单对象）。

2、分工明确，而且扩展点相当灵活，可以很容易扩展，虽然几乎不需要。

3、由于命令对象就是一个 POJO，无需继承框架特定 API，可以使用命令对象直接作为业务对象。

4、和 Spring 其他框架无缝集成，是其它 Web 框架所不具备的。

5、可适配，通过 HandlerAdapter 可以支持任意的类作为处理器。

6、可定制性，HandlerMapping、ViewResolver 等能够非常简单的定制。

7、功能强大的数据验证、格式化、绑定机制。

8、利用 Spring 提供的 Mock 对象能够非常简单的进行 Web 层单元测试。

9、本地化、主题的解析的支持，使我们更容易进行国际化和主题的切换。

10、强大的 JSP 标签库，使 JSP 编写更容易。

………………还有比如RESTful风格的支持、简单的文件上传、约定大于配置的契约式编程支持、基于注解的零配

置支持等等。



# 四、SpringMVC的执行流程（重点）

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170625.png" alt="image-20200513141457038" style="zoom: 100%;" />

（1）用户发送请求至前端控制器DispatcherServlet；

（2） DispatcherServlet收到请求后，调用HandlerMapping处理器映射器，请求获取Handle处理器；

（3）处理器映射器根据**请求url**找到具体的处理器，生成**处理器对象Handler及处理器拦截器(如果有则生成)**一并返回给DispatcherServlet；

（4）DispatcherServlet 调用 HandlerAdapter处理器适配器；

（5）HandlerAdapter 经过适配调用 具体处理器(Handler，也叫后端控制器)，并执行处理器Handler；

（6）Handler执行完成返回ModelAndView给HandlerAdapter；

（7）HandlerAdapter将Handler执行结果ModelAndView返回给DispatcherServlet；

（8）DispatcherServlet将ModelAndView传给ViewResolver视图解析器进行解析，根据逻辑视图名解析成真正的视图(jsp)；

（9）ViewResolver解析后返回具体View；

（10）DispatcherServlet对View进行渲染视图（即将模型数据填充至视图中）

（11）DispatcherServlet响应用户。





# 五、SpringMVC 和 和 Struts2  的优略分析

## 1、共同点：

它们都是表现层框架，都是基于 MVC 模型编写的。

它们的底层都离不开原始 ServletAPI。

它们处理请求的机制都是一个核心控制器。

## 2、区别：

Spring MVC 的入口是 Servlet, 而 Struts2 是 Filter

Spring MVC 是基于方法设计的，而 Struts2 是基于类，Struts2 每次执行都会创建一个动作类。所以 Spring MVC 会稍微比 Struts2 快些。

Spring MVC 使用更加简洁,同时还支持 JSR303, 处理 ajax 的请求更方便

(JSR303 是一套 JavaBean 参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注解加在我们 JavaBean 的属性上面，就可以在需要校验的时候进行校验了。)

Struts2 的 OGNL 表达式使页面的开发效率相比 Spring MVC 更高些，但执行效率并没有比 JSTL 提升，尤其是 struts2 的表单标签，远没有 html 执行效率高。





