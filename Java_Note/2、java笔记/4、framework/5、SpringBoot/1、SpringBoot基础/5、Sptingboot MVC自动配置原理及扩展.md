[TOC]

# Sptingboot MVC扩展及自动配置原理

阅读 [Springboot官方文档](https://docs.spring.io/spring-boot/docs/2.2.5.RELEASE/reference/htmlsingle/#boot-features-spring-mvc-auto-configuration)



## 一、文档介绍

Spring Boot为Spring MVC提供了自动配置，它可以很好地与大多数应用程序一起工作。

### 1、自动配置在Spring默认设置的基础上添加了以下功能：

-  包含视图解析器；
- 支持静态资源文件夹的路径，以及webjars；
- 自动注册了Converter：转换器，这就是我们网页提交数据到后台自动封装成为对象的东西，比如把"1"字符串自动转换为int类型
- Formatter：【格式化器，比如页面给我们了一个2019-8-10，它会给我们自动格式化为Date对象】
- 首页定制、图标定制、定义错误代码生成规则
- 初始化数据绑定器：帮我们把请求数据绑定到JavaBean中！

### 2、保留Spring Boot MVC功能（官方推荐）

如果您希望保留Spring Boot MVC功能，并且希望添加其他MVC配置（拦截器、格式化程序、视图控制器和其他功能），则可以**添加自己的@configuration类**，类型为webmvcconfiguer，但**不添加**@EnableWebMvc。如果希望提供RequestMappingHandlerMapping、RequestMappingHandlerAdapter或ExceptionHandlerExceptionResolver的自定义
实例，则可以声明WebMVCregistrationAdapter实例来提供此类组件。

以视图解析器为例，重写addViewControllers

```java
@Configuration
public class helloController implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //浏览器发送 /hello ， 就会跳转到hello页面；
        //  http://localhost:8080/hello
        registry.addViewController("/hello").setViewName("hello");
    }
}
```

### 3、全面接管SpringMVC

#### 3.1 具体实现

如果您想完全控制SpringBoot  MVC，可以添加自己的@Configuration，并用@EnableWebMvc进行注释。

```java
@Configuration
@EnableWebMvc
public class helloController implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        //浏览器发送 /hello ， 就会跳转到hello页面；
        //  http://localhost:8080/hello
        registry.addViewController("/hello").setViewName("hello");
    }
}
```

#### 3.2 原理

使用@EnableWebMvc会导入DelegatingWebMvcConfiguration类，其继承了WebMvcConfigurationSupport ，会使WebMvcAutoConfiguration 自动配置类失效

```java
// 这个注解的意思就是：容器中没有这个组件的时候，这个自动配置类才生效
@ConditionalOnMissingBean(WebMvcConfigurationSupport.class)
public class WebMvcAutoConfiguration {
}
```





## 二、MVC自动配置原理（视图解析器为例）

### 1、ContentNegotiatingViewResolver： 内容协商视图解析器 

自动配置了ViewResolver，就是我们之前学习的SpringMVC的视图解析器；

即根据方法的返回值取得视图对象（View），然后由视图对象决定如何渲染（转发，重定向）。

我们去看看这里的源码：我们找到 WebMvcAutoConfiguration ， 然后搜索ContentNegotiatingViewResolver。找到里面的resolveViewName方法。该方法通过getCandidateViews把所有的视图解析器拿来，进行while循环，挨个解析！

得出结论：**ContentNegotiatingViewResolver 这个视图解析器就是用来组合所有的视图解析器的** 



### 2、自定义视图解析器

我们如果想要使用自己定制化的东西，我们只需要给容器中添加这个组件就好了！剩下的事情SpringBoot就会帮我们做了！

```java
@Configuration  //添加Configuration注解
public class helloController implements WebMvcConfigurer { //实现WebMvcConfigurer接口
  
    @Bean //放到bean中
    public ViewResolver myViewResolver(){
        return new MyViewResolver();
    }

	//我们写一个静态内部类，视图解析器就需要实现ViewResolver接口
	private static class MyViewResolver implements ViewResolver{
        @Override
        public View resolveViewName(String s, Locale locale) throws Exception {
            return null;
        }
	}
}
```















## 参考文章

[狂神说SpringBoot12：MVC自动配置原理](https://mp.weixin.qq.com/s?__biz=Mzg2NTAzMTExNg==&mid=2247483819&idx=1&sn=b9009aaa2a9af9d681a131b3a49d8848&scene=19#wechat_redirect)





