

# IoC （Inversion of control ）：控制反转/反转控制

# 一、基本介绍

## 1、定义

它是一种**设计思想**不是一个技术实现。

描述的是：Java 开发领域对象的创建以及管理的问题。

**IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个 Map（key，value）,Map 中存放的是各种对象。**

## 2、思想

不通过 new 关键字来创建对象，而是IoC 容器(Spring 框架) 通过**反射**帮助我们实例化对象。我们需要哪个对象，直接从 IoC 容器里面过去即可

## 3、控制反转

**控制** ：指的是对象创建（实例化、管理）的控制权

**反转** ：控制权交给外部环境（Spring 框架、IoC 容器）

 即将原本在程序中手动创建对象的控制权，交由Spring框架来管理

## 4、好处

- 对象之间的耦合度或者说依赖程度降低；
- 资源变的容易管理；





# 二、IoC容器

## 1、BeanFactory

简单的容器，就是一个管理 Bean 的工厂，它主要负责初始化各种 Bean，并调用它们的生命周期方法。

## 2、ApplicationContext

ApplicationContext 是 BeanFactory 的子接口，也被称为应用上下文。

### 2.1 更加强大的功能

1) 国际化i18n（MessageSource）

2) 访问资源，如URL和文件（ResourceLoader）

3) 载入多个（有继承关系）上下文 ，使得每一个上下文都专注于一个特定的层次，比如应用的web层  

4) 消息发送、响应机制（ApplicationEventPublisher）

5) AOP（拦截器）

### 2.2  常用实现类

|     ApplicationContext常用实现类      |                             作用                             |
| :-----------------------------------: | :----------------------------------------------------------: |
|  AnnotationConfigApplicationContext   | 从一个或多个基于java的配置类中加载上下文定义，适用于java注解的方式。 |
|    ClassPathXmlApplicationContext     | 从类路径下的一个或多个xml配置文件中加载上下文定义，适用于xml配置的方式。 |
|    FileSystemXmlApplicationContext    | 从文件系统下的一个或多个xml配置文件中加载上下文定义，也就是说系统盘符中加载xml配置文件。 |
| AnnotationConfigWebApplicationContext |            专门为web应用准备的，适用于注解方式。             |
|       XmlWebApplicationContext        | 从web应用下的一个或多个xml配置文件加载上下文定义，适用于xml配置方式。 |





## 3、二者区别

### 3.1  创建Bean方式

BeanFactroy采用的是**延迟加载形式**来注入Bean的，即只有在使用到某个Bean时(**调用getBean()**)，才对该Bean进行加载实例化，这样，我们就不能发现一些存在的Spring的配置问题。**（适合多例对象）**

而ApplicationContext则相反，它是在**容器启动时，一次性创建了所有的Bean**。这样，在容器启动时，我们就可以发现Spring中存在的配置错误。 **（适合单例对象）**

相对于基本的BeanFactory，ApplicationContext 唯一的不足是占用内存空间。当应用程序配置Bean较多时，程序启动较慢。

### 3.2 BeanPostProcessor

BeanFactory和ApplicationContext都支持BeanFactoryPostProcessor的使用，但两者之间的区别是：BeanFactory需要手动注册，而ApplicationContext则是自动注册。（Applicationcontext比 beanFactory 加入了一些更好使用的功能。而且 beanFactory 的许多功能需要通过编程实现而 Applicationcontext 可以通过配置实现。比如后处理 bean ， Applicationcontext 直接配置在配置文件即可而 beanFactory 这要在代码中显示的写出来才可以被容器识别。 ）



### 3.3 利用MessageSource进行国际化  

​    BeanFactory是不支持国际化功能的，因为BeanFactory没有扩展Spring中MessageResource接口。相反，由于ApplicationContext扩展了MessageResource接口，因而具有消息处理的能力(i18N)，



beanFactory主要是面对与 spring 框架的基础设施，面对 spring 自己。而 Applicationcontex 主要面对与 spring 使用的开发者。基本都会使用 Applicationcontex 并非 beanFactory 。













