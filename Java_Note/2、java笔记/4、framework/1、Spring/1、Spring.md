# Spring
--------------------------------------------------------------------------------
# 一、Spring介绍
Spring是分层的 Java SE/EE应用 full-stack 轻量级开源框架，以 IoC（Inverse Of Control：反转控制）和 AOP（Aspect Oriented Programming：面向切面编程）为内核，提供了展现层 SpringMVC 和持久层 Spring JDBC 以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，逐渐成为使用最多的Java EE 企业应用开源框架。

# 二、Spring体系结构
![image-20200523172420576](E:\black user\Java\有道云截图\image-20200523172420576.png)

- **Spring Core：** 基础,可以说 Spring 其他所有的功能都需要依赖于该类库。主要提供 IoC 依赖注入功能。
- **Spring Aspects** ： 该模块为与AspectJ的集成提供支持。
- **Spring AOP** ：提供了面向切面的编程实现。
- **Spring JDBC** : Java数据库连接。
- **Spring JMS** ：Java消息服务。
- **Spring ORM** : 用于支持Hibernate等ORM工具。
- **Spring Web** : 为创建Web应用程序提供支持。
- **Spring Test** : 提供了对 JUnit 和 TestNG 测试的支持。



# 三、组成核心

IOC:降低程序之间的耦合
		AOP:面向切面编程，是面向对象开发的一种补充，它允许开发人员在不改变原来模型的以满足新的需求，例如，开发人员可以在不改变原来业务逻辑模型的基础可以进行动态的增加日志，安全或异常处理功能。


