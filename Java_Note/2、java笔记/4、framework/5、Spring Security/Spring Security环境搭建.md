#  Spring Security环境搭建
--------------------------------------------------------------------------------

# 一、基本介绍

是 Spring 项目组中用来提供安全认证服务的框架。


# 二、环境搭建（在xml设置登陆用户和密码）
1、导入jar包

```
<dependencies>
<dependency>
<groupId>org.springframework.security</groupId>
<artifactId>spring-security-web</artifactId>
<version>5.0.1.RELEASE</version>
</dependency>
<dependency>
<groupId>org.springframework.security</groupId>
<artifactId>spring-security-config</artifactId>
<version>5.0.1.RELEASE</version>
</dependency>
</dependencies>
```


2、spring-security.xml配置（自带登陆页面）

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:security="http://www.springframework.org/schema/security"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/security
http://www.springframework.org/schema/security/spring-security.xsd">

    <security:http auto-config="true" use-expressions="false">
    
<!-- intercept-url定义一个过滤规则 pattern表示对哪些url进行权限控制，ccess属性表示在请求对应的URL时需要什么权限，
默认配置时它应该是一个以逗号分隔的角色列表，请求的用户只需拥有其中的一个角色就能成功访问对应的URL -->
    <security:intercept-url pattern="/**" access="ROLE_USER" />

<!-- auto-config配置后，不需要在配置下面信息 <security:form-login /> 定义登录表单信息<security:http-basic/> <security:logout /> -->
    </security:http>

    <security:authentication-manager>
        <security:authentication-provider>
            <security:user-service>
                <security:user name="user" password="{noop}user  authorities="ROLE_USER" />
                <security:user name="admin" password="{noop}admin" authorities="ROLE_ADMIN" />
            </security:user-service>
        </security:authentication-provider>
    </security:authentication-manager>
</beans>
```



三、自定义页面设置

```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:security="http://www.springframework.org/schema/security"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans.xsd
http://www.springframework.org/schema/security
http://www.springframework.org/schema/security/spring-security.xsd">
<!-- 配置不过滤的资源（静态资源及登录相关） -->
<security:http security="none" pattern="/login.html" />
<security:http security="none" pattern="/failer.html" />
<security:http auto-config="true" use-expressions="false">
<!-- 配置资料连接，表示任意路径都需要ROLE_USER权限 -->
<security:intercept-url pattern="/**" access="ROLE_USER" />
<!-- 自定义登陆页面，login-page 自定义登陆页面 authentication-failure-url 用户权限校验失败之
后才会跳转到这个页面，如果数据库中没有这个用户则不会跳转到这个页面。
default-target-url 登陆成功后跳转的页面。 注：登陆页面用户名固定 username，密码
password，action:login -->
<security:form-login login-page="/login.html"
login-processing-url="/login" username-parameter="username"
password-parameter="password" authentication-failure-url="/failer.html"
default-target-url="/success.html"
/>
<!-- 登出， invalidate-session 是否删除session logout-url：登出处理链接 logout-success-
url：登出成功页面
注：登出操作 只需要链接到 logout即可登出当前用户 -->
<security:logout invalidate-session="true" logout-url="/logout"
logout-success-url="/login.jsp" />
<!-- 关闭CSRF,默认是开启的 -->
<security:csrf disabled="true" />
</security:http>
<security:authentication-manager>
<security:authentication-provider>
<security:user-service>
<security:user name="user" password="{noop}user"
authorities="ROLE_USER" />
<security:user name="admin" password="{noop}admin"
authorities="ROLE_ADMIN" />
</security:user-service>
</security:authentication-provider>
</security:authentication-manager>
</beans>
```

# 三、使用数据库认证
1、在Spring Security中如果想要使用数据进行认证操作，有很多种操作方式，这里我们介绍使用UserDetails、
UserDetailsService来完成操作。
### 1）UserDetails

```
public interface UserDetails extends Serializable {
Collection<? extends GrantedAuthority> getAuthorities();
String getPassword();
String getUsername();
boolean isAccountNonExpired();
boolean isAccountNonLocked();
boolean isCredentialsNonExpired();
boolean isEnabled();
}
```
UserDetails是一个**接口**，我们可以认为UserDetails作用是于封装当前进行认证的==用户信息==，但由于其是一个
接口，所以我们可以对其进行实现，也可以使用Spring Security提供的一个UserDetails的==实现类User==来完成
操作

```
public class User implements UserDetails, CredentialsContainer
```
使用实例：

```
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        UserInfo userInfo = null;


        try {
            userInfo = userDao.findByUsername(username);
        } catch (Exception e) {
            e.printStackTrace();
        }
        //密码是自己手动输入到数据库的所以未经加密在spring security解析密码时就会产生错误

        //处理自己的用户对象封装成UserDetails
        //  User user=new User(userInfo.getUsername(),"{noop}"+userInfo.getPassword(),getAuthority(userInfo.getRoles()));
        User user = new User(userInfo.getUsername(), new BCryptPasswordEncoder().encode(userInfo.getPassword()), userInfo.getStatus() == 0 ? false : true, true, true, true, getAuthority(userInfo.getRoles()));

        return user;
    }
```




### 2）UserDetailsService
对实现方法的认证
```
public interface UserDetailsService {
UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
}
```






