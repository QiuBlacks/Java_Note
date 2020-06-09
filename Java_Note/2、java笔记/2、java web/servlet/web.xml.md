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

