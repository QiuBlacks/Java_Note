# 集成Swagger

集成Swagger**自动生成API文档**

# 一、基本介绍

> 相信无论是前端还是后端开发，都或多或少地被接口文档折磨过。前端经常抱怨后端给的接口文档与实际情况不一致。后端又觉得编写及维护接口文档会耗费不少精力，经常来不及更新。其实无论是前端调用后端，还是后端调用后端，都期望有一个好的接口文档。但是这个接口文档对于程序员来说，就跟注释一样，经常会抱怨别人写的代码没有写注释，然而自己写起代码起来，最讨厌的，也是写注释。所以仅仅只通过强制来规范大家是不够的，随着时间推移，版本迭代，接口文档往往很容易就跟不上代码了。

## 1、前后端分离

- 前端 -> 前端控制层、视图层
- 后端 -> 后端控制层、服务层、数据访问层
- 前后端通过API进行交互
- 前后端相对独立且松耦合

## 2、产生的问题

- 前后端集成，前端或者后端无法做到“及时协商，尽早解决”，最终导致问题集中爆发

## 3、解决方案

- 首先定义schema [ 计划的提纲 ]，并实时跟踪最新的API，降低集成风险



## 4、Swagger

解决方案用的人多了，就成了标准的规范，这就是Swagger的由来。通过这套规范，你只需要按照它的规范去定义接口及接口相关的信息。再通过Swagger衍生出来的一系列项目和工具，就可以做到生成各种格式的接口文档，生成多种语言的客户端和服务端的代码，以及在线接口调试页面等等。这样，如果按照新的开发模式，在开发新版本或者迭代版本的时候，只需要更新Swagger描述文件，就可以自动生成接口文档和客户端服务端代码，做到调用端代码、服务端代码以及接口文档的一致性。

Spring迅速将Swagger规范纳入自身的标准，建立了Spring-swagger项目，后面改成了现在的**Springfox**。通过在项目中引入Springfox，可以扫描相关的代码，生成该描述文件，进而生成与代码一致的接口文档和客户端代码。这种通过代码生成接口文档的形式，在后面需求持续迭代的项目中，显得尤为重要和高效。

- 号称世界上最流行的API框架
- Restful Api 文档在线自动生成器 => API 文档 与API 定义同步更新
- 直接运行，在线测试API
- 支持多种语言 （如：Java，PHP等）
- 官网：https://swagger.io/



# 二、Springboot集成Swagger

## 1、环境搭建

### 1.1 导入依赖

```
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger2 -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger2</artifactId>
   <version>2.9.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/io.springfox/springfox-swagger-ui -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.9.2</version>
</dependency>
```

### 1.2 编写Controller看是否启动成功

```
@RestController
public class helloController {

    @RequestMapping("/hello")
    public String hello(){
        return "hello";
    }
}
```

### 1.3 测试Swagger

```
@Configuration //配置类
@EnableSwagger2// 开启Swagger2的自动配置
public class SwaggerConfig {

    @Bean //配置docket以配置Swagger具体参数
    public Docket docket() {
       
    }
}
```

访问  http://localhost:8080/swagger-ui.html 



## 2、配置Swagger

Swagger实例Bean是Docket，所以通过配置Docket实例来配置Swaggger

可以通过apiInfo()属性配置文档信息

```java
@Configuration //配置类
@EnableSwagger2// 开启Swagger2的自动配置
public class SwaggerConfig {

    @Bean //配置docket以配置Swagger具体参数
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo());
    }

    //配置文档信息
    private ApiInfo apiInfo() {
        Contact contact = new Contact("qiu", "https://github.com/QiuBlacks/Java_Note", "qrj15521026074@163.com");
        return new ApiInfo(
                "邱的Swagger学习", // 标题
                "学习演示如何配置Swagger", // 描述
                "v1.0", // 版本
                "https://github.com/QiuBlacks/Java_Note", // 组织链接
                contact, // 联系人信息
                "Apach 2.0 许可", // 许可
                "许可链接", // 许可连接
                new ArrayList<>()// 扩展
        );
    }

}
```

可以看到里面的文件信息都被更改了

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200920165159.png" alt="image-20200920165156894" style="zoom:67%;" />





## 3、配置扫描路径和接口

### 3.1  select().apis :扫描固定接口 

构建Docket时通过select()方法配置怎么扫描接口

```java
 @Bean //配置docket以配置Swagger具体参数
    public Docket docket() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                // 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
                .apis(RequestHandlerSelectors.basePackage("com.qiu.swagger.controller"))
                .build();

    }
```

可以看到那个basic-error-controller类不见了

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200920165727.png" alt="image-20200920165727772" style="zoom:67%;" />

apis的RequestHandlerSelectors除了basePackage属性外，还有其他属性：

```java
any() // 扫描所有，项目中的所有接口都会被扫描到
none() // 不扫描接口
// 通过方法上的注解扫描，如withMethodAnnotation(GetMapping.class)只扫描get请求
withMethodAnnotation(final Class<? extends Annotation> annotation)
// 通过类上的注解扫描，如.withClassAnnotation(Controller.class)只扫描有controller注解的类中的接口
withClassAnnotation(final Class<? extends Annotation> annotation)
basePackage(final String basePackage) // 根据包路径扫描接口
```



### 3.2   select().paths()：路径过滤

```java
@Bean //配置docket以配置Swagger具体参数
public Docket docket() {
    return new Docket(DocumentationType.SWAGGER_2)
        .apiInfo(apiInfo())
        .select()
        // 通过.select()方法，去配置扫描接口,RequestHandlerSelectors配置如何扫描接口
        .apis(RequestHandlerSelectors.any())
        // 配置如何通过path过滤,即这里只扫描请求以/qiu开头的接口
        .paths(PathSelectors.ant("/qiu"))
        .build();

}
```

PathSelectors的属性还有

```java
any() // 任何请求都扫描
none() // 任何请求都不扫描
regex(final String pathRegex) // 通过正则表达式控制
ant(final String antPattern) // 通过ant()控制
```



## 4、配置Swagger开关

