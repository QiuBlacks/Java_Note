# Mybatis的连接池实现

# 一、连接池介绍

## 1、连接池：

​	我们在实际开发中都会使用连接池。

​	因为它可以减少我们获取连接所消耗的时间。

![image-20200513133455836](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170038.png)



## 2、mybatis中的连接池

### 2.1 配置的位置

主配置文件SqlMapConfig.xml中的dataSource标签，type属性就是表示采用何种连接池方式。

### 2.2  type属性的取值

mybatis连接池提供了3种方式的配置：

1）POOLED	      采用传统的javax.sql.DataSource规范中的连接池，mybatis中有针对规范的实现		

![image-20200513133517405](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170039.png)



2）UNPOOLED 		采用传统的获取连接的方式，虽然也实现Javax.sql.DataSource接口，但是并没有使用池的思想。

3）JNDI			 采用服务器提供的JNDI技术实现，来获取DataSource对象，不同的服务器所能拿到DataSource是不一样。

​				 注意：如果不是web或者maven的war工程，是不能使用的。

​				 我们课程中使用的是tomcat服务器，采用连接池就是dbcp连接池。



### 2.3  区别

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170040.png" alt="image-20200513133536224" style="zoom: 200%;" />