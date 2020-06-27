[TOC]



# Java

----------------------------------------------------------------
# 一、基本介绍
## 1、分类
&emsp; &emsp;   Java SE（Java Platform，Standard Edition）。Java SE 以前称为 J2SE。它允许开发和部署在桌面、服务器、嵌入式环境和实时环境中使用的 Java 应用程序。Java SE 包含了支持 Java Web 服务开发的类，并为 Java Platform，Enterprise Edition（Java EE）提供基础。<br/>
 &emsp; &emsp;  	 Java EE（Java Platform，Enterprise Edition）。这个版本以前称为 J2EE。企业版本帮助开发和部署可移植、健壮、可伸缩且安全的服务器端 Java 应用程序。Java EE 是在 Java SE 的基础上构建的，它提供 Web 服务、组件模型、管理和通信 API，可以用来实现企业级的面向服务体系结构（service-oriented architecture，SOA）和 Web 2.0 应用程序。<br>
 &emsp; &emsp;  Java ME（Java Platform，Micro Edition）。这个版本以前称为 J2ME。Java ME 为在移动设备和嵌入式设备（比如手机、PDA、电视机顶盒和打印机）上运行的应用程序提供一个健壮且灵活的环境。Java ME 包括灵活的用户界面、健壮的安全模型、许多内置的网络协议以及对可以动态下载的连网和离线应用程序的丰富支持。基于 Java ME 规范的应用程序只需编写一次，就可以用于许多设备，而且可以利用每个设备的本机功能。
## 2、特点
- ###  跨平台性(核心优势)
&emsp; &emsp;"一次编译，随处运行"    <br/>
&emsp; &emsp;软件可以不受计算机硬件和操作系统的约束而在任意计算机环境下正常运行。 <br/>
&emsp; &emsp;Java自带的虚拟机很好地实现了跨平台性。 Java源程序代码经过编译后生成二进制的字节码是与平台无关的，但是可被Java虚拟机识别的一种机器码指令。 Java虚拟机提供了一个字节码到底层硬件平台及操作系统的屏障，使得Java语言具备跨平台性。
- ###  面向对象
&emsp; &emsp;面向对象技术使得应用程序的开发变得简单易用，节省代码。Java是一种面向对象的语言，也继承了面向对象的诸多好处，如代码扩展、代码复用等。
- ### 多线程
&emsp; &emsp;多线程是指允许一个应用程序同时存在两个或两个以上的线程，用于支持事务并发和多任务处理。 Java除了内置的多线程技术之外，还定义了一些类、方法等来建立和管理用户定义的多线程。
- ### 安全性
　&emsp; &emsp;安全性可以分为四个层面，即语言级安全性、编译时安全性、运行时安全性、可执行代码安全性。语言级安全性指Java的数据结构是完整的对象，这些封装过的数据类型具有安全性。编译时要进行Java语言和语义的检查，保证每个变量对应一个相应的值，编译后生成Java类。运行时Java类需要类加载器载入www jxzxmr net，并经由字节码校验器校验之后才可以运行。 Java类在网络上使用时，对它的权限进行了设置，保证了被访问用户的安全性。
- ### 高性能
&emsp; &emsp;Java语言本身发展中通过虚拟机的优化提升了几十倍运行效率。比如，通过JIT(JUST IN TIME)即时编译技术提高运行效率。 
- ### 分布式
 &emsp; &emsp;Java是为Internet的分布式环境设计的，因为它能够处理TCP/IP协议。事实上，通过URL访问一个网络资源和访问本地文件是一样简单的。Java还支持远程方法调用(RMI,Remote Method Invocation)，使程序能够通过网络调用方法


# 二、JDK(Java  Development Kit)、 JRE（Java Runtime Environment ）
- JDK是Java开发工具包，包含java开发工具+JRE+类库，它能够创建和编译程序。
- JRE是Java运行时环境，提供 jvm+类库；Java虚拟机、库函数、运行Java应用程序所必须的文件。



#  三、知识体系

![image](https://www.pdai.tech/_images/java_basic.png)






