# SpringBoot 自定义手写starter

# 一、基础知识

### 1、SpringBoot starter机制

　　SpringBoot中的starter是一种非常重要的机制，能够抛弃以前繁杂的配置，将其统一集成进starter，应用者只需要在maven中引入starter依赖，SpringBoot就能自动扫描到要加载的信息并启动相应的默认配置。starter让我们摆脱了各种依赖库的处理，需要配置各种信息的困扰。SpringBoot会自动通过classpath路径下的类发现需要的Bean，并注册进IOC容器。SpringBoot提供了针对日常企业应用研发各种场景的spring-boot-starter依赖模块。所有这些依赖模块都遵循着约定成俗的默认配置，并允许我们调整这些配置，即遵循“约定大于配置”的理念。

### 2、为什么要自定义starter

　　在我们的日常开发工作中，经常会有一些独立于业务之外的配置模块，我们经常将其放到一个特定的包下，然后如果另一个工程需要复用这块功能的时候，需要将代码硬拷贝到另一个工程，重新集成一遍，麻烦至极。如果我们将这些可独立于业务代码之外的功配置模块封装成一个个starter，复用的时候只需要将其在pom中引用依赖即可，SpringBoot为我们完成自动装配，简直不要太爽。

### 3、自定义starter的案例

　　以下案例由笔者工作中遇到的部分场景

　　▲ 动态数据源。

　　▲ 登录模块。

　　▲ 基于AOP技术实现日志切面。

　　。。。。。。

### 4、自定义starter的命名规则

　　SpringBoot提供的starter以`spring-boot-starter-xxx`的方式命名的。官方建议自定义的starter使用`xxx-spring-boot-starter`命名规则。以区分SpringBoot生态提供的starter。





# 二、手写starter

一个完整的Spring Boot Starter可能包含以下组件：

- **autoconfigure**模块：包含自动配置的代码
- **starter**模块：提供对**autoconfigure**模块的依赖，以及一些其它的依赖

（PS：如果你不需要区分这两个概念的话，也可以将自动配置代码模块与依赖管理模块合并成一个模块）



<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201115172134.png" alt="image-20201115172125419" style="zoom:80%;" />

## 1、创建工程

 **hello-spring-boot-starter** ： Maven项目

 **hello-spring-boot-starter-autoconfigurer**：springboot项目

同时删除 启动类、resources下的文件，test文件

## 2、导入依赖

 **hello-spring-boot-starter** ： Maven项目

导入hello-spring-boot-starter-autoconfigure的依赖

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.qiu</groupId>
    <artifactId>hello-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>jar</packaging>
    <dependencies>
        <dependency>
            <groupId>com.qiu</groupId>
            <artifactId>hello-spring-boot-starter-autoconfigure</artifactId>
            <version>0.0.1-SNAPSHOT</version>
        </dependency>

    </dependencies>


</project>
```

 **hello-spring-boot-starter-autoconfigurer**：springboot项目

注意：

​	要把springboot项目里面的<plugin> 标签删除

​	打包方式：<packaging>jar</packaging>

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<groupId>com.qiu</groupId>
	<artifactId>hello-spring-boot-starter-autoconfigure</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>hello-spring-boot-starter-autoconfigure</name>
	<description>Demo project for Spring Boot</description>



	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>


		<!--实现自动化配置-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-autoconfigure</artifactId>
			<version>2.1.4.RELEASE</version>
		</dependency>

		<!--负责从properties文件中读取配置信息-->
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-configuration-processor</artifactId>
			<version>2.1.4.RELEASE</version>
		</dependency>
	</dependencies>


</project>
```

## 3、编写配置信息类

### 1）配置类 HelloServiceAutoConfiguration

```java
@Configuration
@ConditionalOnWebApplication //web应该生效
@EnableConfigurationProperties(HelloProperties.class)
public class HelloServiceAutoConfiguration {
    @Autowired
    HelloProperties helloProperties;
    @Bean
    public HelloService helloService() {
        HelloService service = new HelloService();
        service.setHelloProperties( helloProperties  );
        return service;
    }
}
```

然后根据配置类进行编写

### 2）HelloService

```java
public class HelloService {

    HelloProperties helloProperties;

    public HelloProperties getHelloProperties() {
        return helloProperties;
    }
    public void setHelloProperties(HelloProperties helloProperties) {
        this.helloProperties = helloProperties;
    }
    public String sayHello(String name ) {
        return helloProperties.getPrefix()+ "-" + name + helloProperties.getSuffix();
    }
}
```



### 3）HelloServiceProperties

```
@ConfigurationProperties(prefix = "gf.hello")
public class HelloProperties {

    private String prefix;
    private String suffix;

    public String getPrefix() {
        return prefix;
    }

    public void setPrefix(String prefix) {
        this.prefix = prefix;
    }

    public String getSuffix() {
        return suffix;
    }

    public void setSuffix(String suffix) {
        this.suffix = suffix;
    }

}
```



在 **resources** 下创建文件夹 **META-INF** 并在 **META-INF** 下创建文件 **spring.factories** ，内容如下：

```
# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
	com.qiu.HelloServiceAutoConfiguration
```

## 4、测试

创建新项目，导入启动器依赖

```
<!-- 引入自定义starter -->
<dependency>
    <groupId>com.gf</groupId>
    	<artifactId>hello-spring-boot-starter</artifactId>
    <version>0.0.1-SNAPSHOT</version>
</dependency>
```

编写HelloController

```
package com.gf.controller;

import com.gf.service.HelloService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @Autowired
    HelloService helloService;

    @GetMapping("/hello/{name}")
    public String hello(@PathVariable(value = "name") String name) {
        return helloService.sayHello( name + " , " );
    }

}
```

application.properties

```
gf.hello.prefix = hi
gf.hello.suffix = what's up man ?
```

我运行项目访问 [http://127.0.0.1:8080/hello/zhangsan](https://links.jianshu.com/go?to=http%3A%2F%2F127.0.0.1%3A8080%2Fhello%2Fzhangsan)，结果如下：