# Springboot集成JPA

## 1、插件报错

打包遇到错误Failed to execute goal org.apache.maven.plugins:maven-surefire-plugin:2.22.2:test

解决办法：修改pom.xml文件

```
<plugins>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-surefire-plugin</artifactId>
		<version>2.19.1</version>
	</plugin>
</plugins>
```



## 2、报错Cannot resolve table

在编写实体类时，使用@table将实体类和数据表关联在一起，会报错

解决办法：根据智能提示Assign Data Sources然后选择相应的数据库可解决

![image-20201126153758339](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201126165603.png)



## 3、自增主键配置

如果你使用的是 MySQL 或者 MariaDB， 你可以在表中设置自增序列，让数据库来帮你处理主键。

但是在 Hibernate 的 @GeneratedValue 注解中，你需要设置为： **@GeneratedValue(strategy = GenerationType.IDENTITY)**，这样你就不会遇到上面提示的数据库没有找到的错误了。

```
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
public Long  id;
```

