# Springboot整合Shiro

主要根据入门案例里的Quickstart代码里面步骤完善

完整代码：F:\java\练习项目\Springboot小项目\springbootshirotest



## 1、实验准备

### 1.1 导入依赖

```
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.4.2</version>
</dependency>
```

### 1.2 前端页面

index.html

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h>首页</h>
    <!--/*@thymesVar id="msg" type=""*/-->
    <div th:text="${msg}"></div>
    <a th:href="@{/user/add}">add</a>
    <a th:href="@{/user/update}">update</a>


</body>
</html>
```

login

```
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h1>登录</h1>
    <hr>

    <form th:action="@{/login}">
        用户名：<input type="text" name="username"><br>
        密码：<input type="password" name="password">
        <br>
        <input type="submit" name="提交">
    </form>
</body>
</html>
```

add

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h>add</h>
</body>
</html>
```

update

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <h> update </h>
</body>
</html>
```

### 1.3 数据库准备

```sql
create table user
(
    id    int(20) auto_increment primary key,
    name  varchar(30) null,
    pwd   varchar(30) null,
    perms varchar(50) null
)charset = utf8;
```





## 2、测试01

设置访问权限，如果没权限则进入登陆界面



### 2.1 编写Shiro配置类

主要编写userRealm、DefaultWebSecurityManager、ShiroFilterFactoryBean三个bean对象

注意：我们可以从下往上写，逐步添加

```java
@Configuration
public class ShiroConfig {

    //ShiroFilterFactoryBean （第三步:连接到前端）
    @Bean
    public ShiroFilterFactoryBean getShiroFilterBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);

        return bean;
    }

    //DefaultWebSecurityManager （第二步：管理realm对象）
    @Bean(name="securityManager")
    public DefaultWebSecurityManager getDefaultWebSecurityManager(@Qualifier("userRealm") UserRealm userRealm){
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        //关联UserRealm
        securityManager.setRealm(userRealm);
        return securityManager;

    }

    //创建realm对象，需要自定义类 (第一步:创建realm对象)
    @Bean(name="userRealm")  //@Bean注解后便被spring托管,不加name属性，默认name值为方法名,这里就加一下吧
    public UserRealm userRealm(){
        return new UserRealm();
    }

}
```

在ShiroFilterFactoryBean方法内添加过滤器，设置访问权限，如果没权限则进入登陆界面

```java
//添加shiro的内置过滤器
/*
anon: 无需认证即可访问
authc: 必须认证才能用
user: 必须拥有 “记住我” 功能才能用
perms: 拥有对某个资源的权限才能用
role: 拥有某个角色权限才能访问
*/

Map<String,String> filterMap = new LinkedHashMap<>();
//拦截
filterMap.put("/user/add","anon");
filterMap.put("/user/update","authc");
//也可使用通配符*
//filterMap.put("/user/*","authc");

bean.setFilterChainDefinitionMap(filterMap);
//若访问时用户未认证，则跳转至登录页面
bean.setLoginUrl("/toLogin");
```



### 2.2 编写UserRealm类

只要进行登陆操作（subject.login）就会执行doGetAuthenticationInfo方法

```java
//自定义UserRealm extends AuthorizingRealm
public class UserRealm extends AuthorizingRealm {
	//授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权");
        return null;
    }

    //认证
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        System.out.println("执行了认证");
        return null;
    }
}
```

### 2.3 编写HellloController进行测试结果

```java
@Controller
public class HellloController {

    @RequestMapping({"/", "/index"})
    public String toIndex(Model model){
        model.addAttribute("msg", "hello");
        return "index";
    }

    @RequestMapping("/user/add")
    public String add(){
        return "/user/add";
    }

    @RequestMapping("/user/update")
    public String update(){
        return "/user/update";
    }

    @RequestMapping("/toLogin")
    public String toLogin(){
        return "login";
    }
}
```



## 3、登陆认证

整体流程：当我们在登陆页面输入账号密码，会执行HelloController的login登陆操作，在使用subject.login(token)时会执行shiro里的UserRealm里的认证方法doGetAuthenticationInfo。

在HelloController中添加登陆认证方法，都是根据入门案例来写的

```java
@RequestMapping("/login")
    public String login(String username,String password,Model model){
        //获取当前用户
        Subject subject = SecurityUtils.getSubject();
        //封装用户的登录数据
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);

        try{
            subject.login(token); //执行登录的方法，如果没有异常就说明ok了
            return "index";
        }catch (UnknownAccountException e){ //用户名不存在
            model.addAttribute("msg","用户名不存在！");
            return "login";
        }catch (IncorrectCredentialsException e){
            model.addAttribute("msg","密码错误！");
            return "login";
        }
    }
```

当我们执行 subject.login登陆操作时，Shiro会执行UserRealm里的认证方法doGetAuthenticationInfo

```java
@Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证");

        //用户名、密码  模拟从数据库中获取
        String name = "root";
        String password = "1111";

        UsernamePasswordToken userToken = (UsernamePasswordToken) token;


        if (!userToken.getUsername().equals(name)){
            return null; //抛出异常 UnknownAccountException
        }
        //密码认证，shiro做~
        return new SimpleAuthenticationInfo("",password,"");

    }
```

## 4、整合Mybatis

目标：使的用户登陆认证数据从数据库中取出

### 4.1 导入依赖

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.18</version>
</dependency>
<dependency>
    <groupId>log4j</groupId>
    <artifactId>log4j</artifactId>
    <version>1.2.17</version>
</dependency>
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.21</version>
</dependency>
```

### 4.2 配置application.yml

这里配置mybatis扫描的包和别名

```yaml
spring:
  datasource:
    username : root
    password: qrj15521026074
    url : jdbc:mysql://localhost:3306/mybatis01?serverTimezone=UTC&useUnicode=true&characterEncoding=utf-8
    driver-class-name: com.mysql.cj.jdbc.Driver
    type: com.alibaba.druid.pool.DruidDataSource # 自定义数据源

    #Spring Boot 默认是不注入这些属性值的，需要自己绑定
    #druid 数据源专有配置
    initialSize: 5
    minIdle: 5
    maxActive: 20
    maxWait: 60000
    timeBetweenEvictionRunsMillis: 60000
    minEvictableIdleTimeMillis: 300000
    validationQuery: SELECT 1 FROM DUAL
    testWhileIdle: true
    testOnBorrow: false
    testOnReturn: false
    poolPreparedStatements: true

    #配置监控统计拦截的filters，stat:监控统计、log4j：日志记录、wall：防御sql注入
    #如果允许时报错  java.lang.ClassNotFoundException: org.apache.log4j.Priority
    #则导入 log4j 依赖即可，Maven 地址：https://mvnrepository.com/artifact/log4j/log4j
    filters: stat,wall,log4j
    maxPoolPreparedStatementPerConnectionSize: 20
    useGlobalDataSourceStat: true
    connectionProperties: druid.stat.mergeSql=true;druid.stat.slowSqlMillis=500

mybatis:
  type-aliases-package: com.qiu.pojo
  mapper-locations: classpath:mapper/*.xml
```

### 4.3配置UserMapper.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.qiu.mapper.UserMapper">

    <select id="queryUserByName" parameterType="String" resultType="com.qiu.pojo.User">
       select * from user where name = #{name};
    </select>

</mapper>
```



### 4.4 编写主要类和接口

#### 编写User类

```
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private Integer id;
    private String name;
    private String pwd;
}
```

#### 编写UserMapper接口

```
@Repository
@Mapper
public interface UserMapper {
    public User queryUserByName(String name);
}
```

#### 编写UserService接口

```
public interface UserService {
    public User queryUserByName(String name);
}
```

#### 编写UserServiceImpl实现类

```
@Service
public class UserServiceImpl implements UserService{

    @Autowired
    UserMapper userMapper;

    @Override
    public User queryUserByName(String name) {
        return userMapper.queryUserByName(name);
    }

}
```



### 4.5 重新编写UserRealm类

让用户数据连接数据库

```java
 @Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证");

      /*
        //用户名、密码  模拟从数据库中获取
        String name = "root";
        String password = "1111";
        if (!userToken.getUsername().equals(name)){
            return null; //抛出异常 UnknownAccountException
        }
        //密码认证，shiro做~
        return new SimpleAuthenticationInfo("",password,"");
        */
        
        UsernamePasswordToken userToken = (UsernamePasswordToken) token;
        //连接数据库
        User user = userService.queryUserByName(userToken.getUsername());
        if (user == null){
            return null; //抛出异常 UnknownAccountException
        }
        //密码认证，shiro做~
        return new SimpleAuthenticationInfo("",user.getPwd(),"");

    }
```

## 5、请求授权

目标：通过从数据库中获取用户权限资源，访问对应权限的方法



### 5.1 重写ShiroConfig

user类增加一个perms权限访问

在ShiroConfig中设置拦截登陆

```java
 @Bean
    public ShiroFilterFactoryBean getShiroFilterBean(@Qualifier("securityManager") DefaultWebSecurityManager defaultWebSecurityManager){
        ShiroFilterFactoryBean bean = new ShiroFilterFactoryBean();
        //设置安全管理器
        bean.setSecurityManager(defaultWebSecurityManager);

        //添加shiro的内置过滤器
        /*
        anon: 无需认证即可访问
        authc: 必须认证才能用
        user: 必须拥有 “记住我” 功能才能用
        perms: 拥有对某个资源的权限才能用
        role: 拥有某个角色权限才能访问
        */

        Map<String,String> filterMap = new LinkedHashMap<>();
        //登陆认证拦截：未登录会跳转登陆页面
        filterMap.put("/user/add","anon");
        filterMap.put("/user/update","authc");
        //也可使用通配符*
        //filterMap.put("/user/*","authc");

        //登陆后授权，正常情况下没有授权会跳转到未授权页面
        filterMap.put("/user/add","perms[user:add]");
        filterMap.put("/user/update","perms[user:update]");

        bean.setFilterChainDefinitionMap(filterMap);

        //若访问时用户未认证，则跳转至登录页面
        bean.setLoginUrl("/toLogin");

        //若访问时用户未被授权，则跳转至未授权页面
        bean.setUnauthorizedUrl("/noauth");

        return bean;
    }
```

### 5.2 重写UserRealm

添加用户的权限，从认证中获取用户信息，取出权限perms

```java
//授权
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        System.out.println("执行了授权");
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //授权操作
        //info.addStringPermission("user:add");

        //拿到当前登录的对象
        Subject subject = SecurityUtils.getSubject();
        User currentUser = (User) subject.getPrincipal(); //拿到user对象
        System.out.println(currentUser.getPerms());
        info.addStringPermission(currentUser.getPerms());

        return info;
    }
```

这里要在认证doGetAuthenticationInfo方法里return时**传入user资源**，使之能在授权方法中使用**获取权限资源**

```java
 @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
        System.out.println("执行了认证");
		//...................
        //这里在登陆后传入user资源
        return new SimpleAuthenticationInfo(user,user.getPwd(),"");

    }
```

### 5.3 在HellloController添加未授权操作

```
@RequestMapping("/noauth")
    @ResponseBody
    public String unauthorized(){
        return "未授权无法访问此页面";
    }
```

## 6、整合thymeleaf

目标：实现特点用户访问特定页面，权限不同，访问到的功能不同

### 6.1 导入依赖

```
 <!--导入thymeleaf依赖-->
 <dependency>
     <groupId>org.thymeleaf</groupId>
     <artifactId>thymeleaf-spring5</artifactId>
     <version>3.0.11.RELEASE</version>
 </dependency>
 <dependency>
     <groupId>org.thymeleaf.extras</groupId>
     <artifactId>thymeleaf-extras-java8time</artifactId>
     <version>3.0.4.RELEASE</version>
 </dependency>

<!--shiro-thymeleaf整合-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 6.2 在ShiroConfig添加整合thymeleaf

```
 //整合ShiroDialect:用来整合shiro thymeleaf
    @Bean
    public ShiroDialect getShiroDialect(){
        return new ShiroDialect();
    }
```



### 6.3 使用thymeleaf重写index登陆页面

导入shiro和thymeleaf的约束

通过shiro:hasPermission指定权限

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org"
      xmlns:shiro="http://www.thymeleaf.org/thymeleaf-extras-shiro" >
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>首页</h1>
    <div th:if="${session.loginUser==null}">
        <a th:href="@{/toLogin}">登录</a>
    </div>
    <p th:text="${msg}"></p>
    <hr>
    <div shiro:hasPermission="user:add">
        <a th:href="@{/user/add}">add</a>
	</div>
    <div shiro:hasPermission="user:update">
        <a th:href="@{/user/update}">update</a>
    </div>

    <a th:href="@{/logout}">注销</a>
</body>
</html>
```



## 7、结果

数据库权限

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201214220801.png" alt="image-20201214220801215" style="zoom:80%;" />

root登陆界面

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201214220738.png" alt="image-20201214220706153" style="zoom:80%;" />

狂神登陆界面

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201214220825.png" alt="image-20201214220825264" style="zoom:80%;" />







## 参考文章

https://www.bilibili.com/video/BV1NE411i7S8?p=6

https://www.cnblogs.com/churujianghudezai/p/12961525.html