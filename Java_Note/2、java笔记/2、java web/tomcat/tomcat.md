# tomcat

官网：http://tomcat.apache.org/

# 一、基础知识

## 1、基本介绍

Tomcat是由Apache开发的一个Servlet容器，是一个免费的开放源代码的**Web 应用服务器**，属于轻量级应用服务器。

实现了对Servlet和JSP的支持，并提供了作为Web服务器的一些特有功能，如Tomcat管理和控制平台、安全域管理和Tomcat阀等。Tomcat包含了一个配置管理工具，也可以通过编辑XML格式的配置文件来进行配置。

## 2、Connector 运行模式

在server.xml里的Connector标签的protocol配置

### 2.1 BIO

```
protocol=”HTTP/1.1”
```



### 2.2 NIO

```
protocol=”org.apache.coyote.http11.Http11NioProtocol”
```



### 2.3 APR： AIO

```
protocol=”org.apache.coyote.http11.Http11AprProtocol”
```



## 3、部署方式

1. 利用Tomcat的自动部署。

   把web应用拷贝到webapps目录。Tomcat在启动时会加载目录下的应用，并将编译后的结果放入work目录下。

2. 使用Manager App控制台部署。

   在tomcat主页点击“Manager App” 进入应用管理控制台，可以指定一个web应用的路径或war文件。

3. 修改conf/server.xml文件部署。

   修改conf/server.xml文件，增加Context节点可以部署应用。

4. 增加自定义的Web部署文件。

   在conf/Catalina/localhost/ 路径下增加 xyz.xml文件，内容是Context节点，可以部署应用。

## 4、工作模式

- 独立的servlet容器，servlet容器是web服务器的一部分；
- 进程内的servlet容器，servlet容器是作为web服务器的插件和java容器的实现，web服务器插件在内部地址空间打开一个jvm使得java容器在内部得以运行。反应速度快但伸缩性不足；
- 进程外的servlet容器，servlet容器运行于web服务器之外的地址空间，并作为web服务器的插件和java容器实现的结合。反应时间不如进程内但伸缩性和稳定性比进程内优；



# 二、底层原理

## 1、顶层结构

Tomcat中最顶层的容器是Server，代表着整个服务器，从上图中可以看出，**一个Server可以包含至少一个Service**，即可以包含多个Service，用于具体提供服务。

Service主要包含两个部分：**Connector和Container**。从上图中可以看出 Tomcat 的心脏就是这两个**核心组件**，他们的作用如下：

- Connector用于处理连接相关的事情，并提供Socket与Request请求和Response响应相关的转化;
- Container用于封装和管理Servlet，以及具体处理Request请求；

![在这里插入图片描述](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201007121538.png)

**一个Tomcat中只有一个Server，一个Server可以包含多个Service，一个Service只有一个Container，但是可以有多个Connectors，**这是因为一个服务可以有多个连接，如同时提供Http和Https链接，也可以提供向相同协议不同端口的连接，示意图如下（Engine、Host、Context下面会说到）：

![image-20200513112325360](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165737.png)

Tomcat服务器的启动是基于一个server.xml文件的，Tomcat启动的时候首先会启动一个Server，Server里面就会启动Service，Service里面就会启动多个"Connector(连接器)"，每一个连接器都在等待客户机的连接，当有用户使用浏览器去访问服务器上面的web资源时，首先是连接到Connector(连接器)，Connector(连接器)是不处理用户的请求的，而是将用户的请求交给一个Engine(引擎)去处理，Engine(引擎)接收到请求后就会解析用户想要访问的Host，然后将请求交给相应的Host，Host收到请求后就会解析出用户想要访问这个Host下面的哪一个Web应用,一个web应用对应一个Context。



## 2、server.xml配置文件详解

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- SHUTDOWN的监听端口: 8005 表示在8005端口监听“SHUTDOWN”命令，如果接收到了就会关闭Tomcat-->
<Server port="8005" shutdown="SHUTDOWN">
  <Listener className="org.apache.catalina.startup.VersionLoggerListener" />
  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />
  <Listener className="org.apache.catalina.core.ThreadLocalLeakPreventionListener" />

  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">
      <!--
		port：tomcat的访问端口  
		protocol：Connector运行模式配置
		-->
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
    <Engine name="Catalina" defaultHost="localhost">
      <Realm className="org.apache.catalina.realm.LockOutRealm">
        <Realm className="org.apache.catalina.realm.UserDatabaseRealm"
               resourceName="UserDatabase"/>
      </Realm>
      <Host name="localhost"  appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
      </Host>
    </Engine>
  </Service>
    
</Server>
```



## 3、Connector和Container

一个请求发送到Tomcat之后，首先经过Service然后会交给我们的Connector，Connector用于接收请求并将接收的请求封装为Request和Response来具体处理，Request和Response封装完之后再交由Container进行处理，Container处理完请求之后再返回给Connector，最后在由Connector通过Socket将处理的结果返回给客户端

### 3.1 Connector架构分析

Connector最底层使用的是Socket来进行连接的，Request和Response是按照HTTP协议来封装的，所以**Connector同时需要实现TCP/IP协议和HTTP协议！**



Connector如何接受请求的？

如何将请求封装成Request和Response的？

封装完之后的Request和Response如何交给Container进行处理的？

Container处理完之后如何交给Connector并返回给客户端的？

![在这里插入图片描述](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201007123350.png)

Connector就是使用ProtocolHandler来处理请求的，不同的ProtocolHandler代表不同的连接类型，比如：Http11Protocol使用的是普通Socket来连接的，Http11NioProtocol使用的是NioSocket来连接的。

其中**ProtocolHandler**由包含了三个部件：**Endpoint、Processor、Adapter。**

1. **Endpoint用来处理底层Socket的网络连接，Processor用于将Endpoint接收到的Socket封装成Request，Adapter用于将Request交给Container进行具体的处理。**
2. Endpoint由于是处理底层的Socket网络连接，因此**Endpoint是用来实现TCP/IP协议**的，而**Processor用来实现HTTP协议**的，Adapter将请求适配到Servlet容器进行具体的处理。
3. Endpoint的抽象实现AbstractEndpoint里面定义的Acceptor和AsyncTimeout两个内部类和一个Handler接口。Acceptor用于监听请求，AsyncTimeout用于检查异步Request的超时，Handler用于处理接收到的Socket，在内部调用Processor进行处理。



### 3.2 Container架构分析

#### 1）Container

Container用于封装和管理Servlet，以及具体处理Request请求，在Container内部包含了4个子容器，结构图如下

![在这里插入图片描述](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201007123542.png)

4个子容器的作用分别是：

- Engine：引擎，用来管理多个站点，一个Service最多只能有一个Engine；
- Host：代表一个站点，也可以叫虚拟主机，通过配置Host就可以添加站点；
- Context：代表一个应用程序，对应着平时开发的一套程序，或者一个WEB-INF目录以及下面的web.xml文件；
- Wrapper：每一Wrapper封装着一个Servlet；

我们的Tomcat中默认的配置下webapps下的每一个文件夹目录都是一个Context，其中ROOT目录中存放着主应用，其他目录存放着子应用，而整个webapps就是一个Host站点。



#### 2）Container如何处理请求的

Container处理请求是使用Pipeline-Valve管道来处理的！

Pipeline-Valve是**责任链模式**，责任链模式是指在一个请求处理的过程中有很多处理者依次对请求进行处理，每个处理者负责做自己相应的处理，处理完之后将处理后的结果返回，再让下一个处理者继续处理。

但是！Pipeline-Valve使用的责任链模式和普通的责任链模式有些不同！区别主要有以下两点：

- 每个Pipeline都有特定的Valve，而且是**在管道的最后一个执行**，这个Valve叫做**BaseValve**，BaseValve是不可删除的；
- 在上层容器的管道的BaseValve中**会调用下层**容器的管道。

Pipeline的处理流程图如下

![在这里插入图片描述](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201007124050.png)

- Connector在接收到请求后会首先调用最顶层容器的Pipeline来处理，这里的最顶层容器的Pipeline就是EnginePipeline（Engine的管道）；
- 在Engine的管道中依次会执行EngineValve1、EngineValve2等等，最后会执行StandardEngineValve，在StandardEngineValve中会调用Host管道，然后再依次执行Host的HostValve1、HostValve2等，最后在执行StandardHostValve，然后再依次调用Context的管道和Wrapper的管道，最后执行到StandardWrapperValve。
- 当执行到StandardWrapperValve的时候，会在StandardWrapperValve中**创建FilterChain**，并调用其**doFilter**方法来处理请求，这个FilterChain包含着我们配置的与请求相匹配的Filter和Servlet，其doFilter方法会依次调用所有的Filter的doFilter方法和Servlet的service方法，这样请求就得到了处理！
- 当所有的Pipeline-Valve都执行完之后，并且处理完了具体的请求，这个时候就可以将返回的结果交给Connector了，Connector在通过Socket的方式将结果返回给客户端。



# 三、

## 1、一个请求在tomcat的过程

一个请求发送到Tomcat之后，首先经过Service然后会交给我们的Connector，Connector用于接收请求并将接收的请求封装为Request和Response来具体处理，Request和Response封装完之后再交由Container进行处理，Container处理完请求之后再返回给Connector，最后在由Connector通过Socket将处理的结果返回给客户端

## 2、tomcat目录结构

tomcat/

├── bin	# 启动脚本，该RPM中不包含catalina.sh文件

│   ├── bootstrap.jar

│   ├── catalina-tasks.xml

│   └── tomcat-juli.jar

├── conf -> /etc/tomcat	# 配置，包括server.xml（主要配置），web应用缺省文件web.xml等

├── lib -> /usr/share/java/tomcat	# tomcat使用的lib库(jar)，会加载到tomcat的classpath中

├── logs -> /var/log/tomcat	# 日志

├── temp -> /var/cache/tomcat/temp	# 临时文件目录

├── webapps -> /var/lib/tomcat/webapps	# web应用

└── work -> /var/cache/tomcat/work	# 工作目录，将翻译的JSP以及其他放到这里