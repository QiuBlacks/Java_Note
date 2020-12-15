```
<servlet>
     <servlet-name>UserServlet</servlet-name> 
	 <servlet-class>com.bjsxt.servlet.UserServlet</servlet-class>  //请求的类名
 </servlet>
		
<servlet-mapping>
  <servlet-name>UserServlet</servlet-name>
  //定义servlet的url访问路径：localhost:8888/user/....
  //即把/a替换
  //可以通过 <form action="user" >访问
	<url-pattern>/user/a</url-pattern>  
 </servlet-mapping>
 
```





**web.xml文件元素的说明**

**web.xml文件中的元素太多。以下是上述web.xml文件中使用的一些元素的说明 -**

**表示整个应用程序。**

**是的子元素，代表servlet。**

**是的子元素，表示servlet的名称。**

**是的子元素，表示servlet的类。**

**是的子元素。它用于映射servlet。**

**是的子元素。客户端使用此模式来调用servlet**

