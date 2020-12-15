# Spring Boot Jpa

# 一、基础知识

## 1、基本介绍

Spring Boot Jpa 是 Spring 基于 ORM 框架、Jpa 规范的基础上封装的一套 Jpa 应用框架，可使开发者用极简的代码即可实现对数据的访问和操作。它提供了包括增删改查等在内的常用功能，且易于扩展！学习并使用 Spring Data Jpa 可以极大提高开发效率！

> Spring Boot Jpa 让我们解脱了 DAO 层的操作，基本上所有 CRUD 都可以依赖于它来实现

hibernate实现了jpa



## 2、功能实现

基本查询也分为两种，一种是 Spring Data 默认已经实现，一种是根据查询的方法来自动解析成 SQL。

Spring Boot Jpa 默认预先生成了一些基本的CURD的方法，例如：增、删、改等等





## 3、常用注解

### @Entity 

表明该类 (UserEntity) 为一个实体类，它默认对应数据库中的表名是user_entity。这里也可以写成

```java
@Entity(name = "xwj_user")
//或者
@Entity
@Table(name = "xwj_user", schema = "test")
//查看@Entity注解，发现其只有一个属性name，表示其所对应的数据库中的表名
```

### @Table

**@Table（name = "自定义的表名"）** 当实体类与其映射的数据库表名不同名时需要使用 @Table注解说明，该标注与 @Entity 注解并列使用，置于实体类声明语句之前

### @Id

注释指定表的主键，它可以有多种生成方式：

### @Transient

表示该属性并非一个到数据库表的字段的映射,ORM框架将忽略该属性.

### @Column

**@Column（name = "自定义字段名"，length = "自定义长度"，nullable = "是否可以空"，unique = "是否唯一"，columnDefinition = "自定义该字段的类型和长度"）**

表示对这个变量所对应的字段名进行一些个性化的设置，例如字段的名字，字段的长度，是否为空和是否唯一等等设置。





导入依赖

```
<dependency>
	<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```





Spring Boot Jpa 默认预先生成了一些基本的CURD的方法，例如：增、删、改等等

1 继承 JpaRepository

```
public interface UserRepository extends JpaRepository<User, Long> {
}
```

2 使用默认方法

```
@Test
public void testBaseQuery() throws Exception {
	User user=new User();
	userRepository.findAll();
	userRepository.findOne(1l);
	userRepository.save(user);
	userRepository.delete(user);
	userRepository.count();
	userRepository.exists(1l);
	// ...
}
```

就不解释了根据方法名就看出意思来