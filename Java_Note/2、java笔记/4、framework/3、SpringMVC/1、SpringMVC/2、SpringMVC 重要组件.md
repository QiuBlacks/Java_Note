## 一、DispatcherServlet（前端控制器）

### 1、介绍



### 2、作用：

Spring MVC 的入口函数。接收请求，响应结果，相当于转发器，中央处理器。有了 DispatcherServlet 减少了其它组件之间的耦合度。用户请求到达前端控制器，它就相当于mvc模式中的c，DispatcherServlet是整个流程控制的中心，由它调用其它组件处理用户的请求，DispatcherServlet的存在降低了组件之间的耦合性。



## 二、HandlerMapping（处理器映射器）



### 2、作用：

根据请求的url查找Handler。

HandlerMapping负责根据用户请求URL找到Handler即处理器（Controller），SpringMVC提供了不同的映射器实现不同的映射方式，例如：配置文件方式，实现接口方式，注解方式等

将在springmvc.xml中配置的或者注解的url与对应的处理类找到并进行存储，用**map<url,handler>**这样的方式来存储。

## 三、HandlerAdapter（处理器适配器）

### 2、作用：

按照特定规则（HandlerAdapter要求的规则）去执行Handler
		通过HandlerAdapter对处理器进行执行，这是==适配器模式==的应用，通过扩展适配器可以对更多类型的处理器进行执行。



## 四、Handler（处理器）：需要工程师开发

### 1、基本介绍

Handler 是继DispatcherServlet前端控制器的==后端控制器==，在DispatcherServlet的控制下Handler对具体的用户请求进行处理。
		由于Handler涉及到具体的用户业务请求，所以一般情况需要工程师根据业务需求开发Handler。

注意：编写Handler时按照HandlerAdapter的要求去做，这样适配器才可以去正确执行Handler





## 五、View resolver（视图解析器）

### 1、基本介绍

View Resolver负责将处理结果生成View视图，View Resolver首先根据逻辑视图名解析成物理视图名即具体的页面地址，再生成View视图对象，最后对View进行渲染将处理结果通过页面展示给用户。 springmvc框架提供了很多的View视图类型，包括：jstlView、freemarkerView、pdfView等。
		一般情况下需要通过页面标签或页面模版技术将模型数据通过页面展示给用户，需要由工程师根据业务需求开发具体的页面。

### 2、作用：

​		进行视图解析，根据==逻辑视图名==解析成真正的视图（view）并返回view



## 六、视图View：需要工程师开发

### 1、基本介绍

View是一个接口，实现类支持不同的View类型（jsp、freemarker、pdf…）

### 2、作用

对视图进行渲染，然后将模型数据填充到requst域中



注意：处理器Handler（也就是我们平常说的Controller控制器）以及视图层view都是需要我们自己手动开发的。其他的一些组件比如：前端控制器DispatcherServlet、处理器映射器HandlerMapping、处理器适配器HandlerAdapter等等都是框架提供给我们的，不需要自己手动开发。



## 参考：

[SpringMVC工作原理](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247484846&idx=1&sn=490014ea65669c1a1e73e25d7b9fa569&chksm=cea24a65f9d5c373d31d6cdd61297db21de63462c1c03c34b7025a0d0b93f1182b2ad7e33cab&token=1082669959&lang=zh_CN&scene=21#wechat_redirect)



