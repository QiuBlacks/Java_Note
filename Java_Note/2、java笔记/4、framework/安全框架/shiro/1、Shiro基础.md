# Shiro基础

# 一、基础知识

## 1、基本介绍

Apache Shiro的设计目标是简化应用程序安全通过直观和易于使用。 Shiro的核心设计模型大多数人如何看待应用程序安全性的上下文中,某人(或东西)与应用程序交互。

软件应用程序通常是设计基于用户故事。 也就是说,你会经常设计用户接口或服务api基于用户如何(或应该)与软件交互。 例如,你可能会说，“如果我的应用程序的用户交互是登录,我将展示他们可以单击一个按钮来查看自己的账户信息。 如果不登录,我将展示一个注册按钮。”

这个例子声明表明应用程序主要是写来满足用户需求和需求。 即使“用户”是另一个软件系统,而不是一个人,你还在写代码,以反映行为基于谁(或什么)目前与您的软件交互。

Shiro反映了这些概念的设计。 通过匹配已经直观的软件开发人员,Apache Shiro依然直观和易于使用的几乎任何应用程序。



## 2、主要概念

Shiro架构有三个主要的概念：Subject，SecurityManager和Realm

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201208161634.jpeg)







## 3、详细的架构

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201208163228.jpeg)















## 参考文章

[Apache Shiro官方构架文档中文翻译](https://blog.csdn.net/liyiming2017/article/details/78201713)

http://shiro.apache.org/architecture.html



