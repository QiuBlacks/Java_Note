# **tomcat**

# **一、体系结构**

![image-20200513112325360](E:\black user\Java\有道云截图\image-20200513112325360.png)



**Tomcat服务器的启动是基于一个server.xml文件的，Tomcat启动的时候首先会启动一个Server，Server里面就会启动Service，Service里面就会启动多个"Connector(连接器)"，每一个连接器都在等待客户机的连接，当有用户使用浏览器去访问服务器上面的web资源时，首先是连接到Connector(连接器)，Connector(连接器)是不处理用户的请求的，而是将用户的请求交给一个Engine(引擎)去处理，Engine(引擎)接收到请求后就会解析用户想要访问的Host，然后将请求交给相应的Host，Host收到请求后就会解析出用户想要访问这个Host下面的哪一个Web应用,一个web应用对应一个Context。**



# **二、工作空间：**

**tomcat/**

**├── bin	# 启动脚本，该RPM中不包含catalina.sh文件**

**│   ├── bootstrap.jar**

**│   ├── catalina-tasks.xml**

**│   └── tomcat-juli.jar**

**├── conf -> /etc/tomcat	# 配置，包括server.xml（主要配置），web应用缺省文件web.xml等**

**├── lib -> /usr/share/java/tomcat	# tomcat使用的lib库(jar)，会加载到tomcat的classpath中**

**├── logs -> /var/log/tomcat	# 日志**

**├── temp -> /var/cache/tomcat/temp	# 临时文件目录**

**├── webapps -> /var/lib/tomcat/webapps	# web应用**

**└── work -> /var/cache/tomcat/work	# 工作目录，将翻译的JSP以及其他放到这里**