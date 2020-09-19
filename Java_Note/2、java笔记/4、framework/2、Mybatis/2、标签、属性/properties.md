# properties

### 1、properties标签

​        可以在标签内部配置连接数据库的信息。也可以通过属性引用外部配置文件信息



### 2、属性

​      2.1  resource属性： 常用的

​            用于指定配置文件的位置，是按照类路径的写法来写，并且必须存在于类路径下。

​      2.2  url属性：

​            是要求按照Url的写法来写地址

​            URL：Uniform Resource Locator 统一资源定位符。它是可以唯一标识一个资源的位置。

​            它的写法：

​                http://localhost:8080/mybatisserver/demo1Servlet

​                协议      主机     端口       URI

​            URI:Uniform Resource Identifier 统一资源标识符。它是在应用中可以唯一定位一个资源的。



```xml
<properties      url="D:/IdeaProjects/day02_eesy_01mybatisCRUD/src/main/resources/jdbcConfig.properties">
     
	<!--
 		<property name="driver" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/eesy_mybatis"></property>
        <property name="username" value="root"></property>
        <property name="password" value="1234"></property>
	-->
</properties>
```





