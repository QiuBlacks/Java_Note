# Spring Security

[中文文档](https://www.docs4dev.com/docs/zh/spring-security/5.1.2.RELEASE/reference)

# 一、基本介绍

## 1、基础知识

### 1.1 两大功能

认证(Authentication)

授权(Authorisation)



### 1.2 底层技术

基于Servlet技术，更确切的说是基于Servlet的Filter过滤器技术

```xml
<beans>
    <security:http auto-config="true" use-expressions="false">
    
<!-- intercept-url定义一个过滤规则 pattern表示对哪些url进行权限控制，access属性表示在请求对应的URL时需要什么权限，
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







# 认证

## 1、登陆过程

用户使用用户名和密码进行登录。

Spring Security将获取到的用户名和密码封装成一个实现了 Authentication 接口的UsernamePasswordAuthenticationToken

将上述产生的 token 对象传递给 AuthenticationManager 进行登录认证。

AuthenticationManager 认证成功后将会返回一个封装了用户权限等信息的 Authentication 对象。

通过调用 SecurityContextHolder.getContext().setAuthentication(...) 将 AuthenticationManager 返回的 Authentication 对象赋予给当前的 SecurityContext。

在认证成功后，跳转到指定的成功页面，默认是应用的根路径。

　此后，用户就可以继续操作去访问其它受保护的资源了，但是在访问的时候将会使用保存在 SecurityContext 中的 Authentication 对象进行相关的权限鉴定。





## 2、认证实现

### 2.1 认证管理器：AuthenticationManager

在 Spring Security 中，**AuthenticationManager** 的默认实现是 **ProviderManager**，而且它不直接自己处理认证请求，

### 2.2 认证提供者：AuthenticationProvider



### 2.3 用户信息的获取和保存：UserDetailsService和UserDetails

UserDetails 是 Spring Security 中一个核心的接口。其中定义了一些可以获取用户名、密码、权限等与认证相关的信息的方法。

登录认证的时候 Spring Security 会通过 UserDetailsService 的 loadUserByUsername() 方法获取对应的 UserDetails 进行加载用户进行认证，认证通过后会将该 UserDetails 赋给认证通过的 Authentication 的 principal，然后再把该 Authentication 存入到 SecurityContext 中。





提到了`UserDetails`就必须得提到`UserDetailsService`, UserDetailsService也是一个接口，且只有一个方法`loadUserByUsername`，他可以用来获取UserDetails。

通常在spring security应用中，我们会自定义一个CustomUserDetailsService来实现UserDetailsService接口，并实现其`public UserDetails loadUserByUsername(final String login);`方法。我们在实现`loadUserByUsername`方法的时候，就可以通过查询数据库（或者是缓存、或者是其他的存储形式）来获取用户信息，然后组装成一个`UserDetails`,(通常是一个`org.springframework.security.core.userdetails.User`，它继承自UserDetails) 并返回。









