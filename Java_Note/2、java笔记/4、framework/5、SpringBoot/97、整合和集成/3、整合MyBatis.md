# 整合MyBatis

官方文档：http://mybatis.org/spring-boot-starter/mybatis-spring-boot-autoconfigure/

Maven仓库地址：https://mvnrepository.com/artifact/org.mybatis.spring.boot/mybatis-spring-boot-starter/2.1.1



所以整合方式有三种？？？Mapper层、serviceimp层注解和非注解



# 二、环境搭建

### 1、导入 MyBatis 所需要的依赖

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.1</version>
</dependency>
```



### 2、创建实体类，导入 Lombok！

/com/kuang/pojo

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private String pwd;
}
```



### 3、创建mapper目录以及对应的 Mapper 接口

/com/kuang/mapper/

//@Mapper : 表示本类是一个 MyBatis 的 Mapper

```java
//@Mapper : 表示本类是一个 MyBatis 的 Mapper
@Mapper
@Repository
public interface UserMapper {
    List<User> queryUserList();
}
```



### 4、创建对应的映射Mapper文件

resources/mybatis/mapper/DepartmentMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.kuang.mapper.UserMapper">

    <select id="queryUserList" resultType="User">
       select * from user;
    </select>

</mapper>
```



### 5、配置application文件，整合Mybatis

```yaml
spring:
  datasource:
    username : root
    password: qrj15521026074
    url : jdbc:mysql://localhost:3306/mybatis01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver


mybatis:
  type-aliases-package: com.kuang.pojo
  mapper-locations: classpath:mybatis/mapper/*.xml
```



### 6、测试

/com/kuang/controller/

```java
@RestController
public class UserController {

    @Autowired
    UserMapper usermapper;

    @GetMapping("/select")
    public List<User> queryUserList(){
        List<User> usersList =  usermapper.queryUserList();
        for(User user : usersList)
            System.out.println(user);
        return usersList;
    }
}
```

