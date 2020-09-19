# Spring MVC统一异常处理

# 一、基本介绍

## 1、异常

系统中异常包括：编译时异常和运行时异常RuntimeException，前者通过捕获异常从而获取异常信息，后者主要通过规范代码开发、测试通过手段减少运行时异常的发生。在开发中，不管是dao层、service层还是controller层，都有可能抛出异常。

在springmvc中，能将所有类型的异常处理从各处理过程解耦出来，既保证了相关处理过程的功能较单一，也实现了异常信息的统一处理和维护。这篇文章主要总结一下SpringMVC中如何统一处理异常。

## 2、图解

![image-20200513150419551](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170721.png)

## 3、Spring MVC 统一异常处理有以下 3 种方式：

- 使用 Spring MVC 提供的简单异常处理器 SimpleMappingExceptionResolver。
- 实现 Spring 的异常处理接口 HandlerExceptionResolver 自定义自己的异常处理器。
- 使用 @ExceptionHandler 注解实现异常处理



























