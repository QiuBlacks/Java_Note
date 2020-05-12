# BUG总结

---
# 一、Bad credentials
### 1、问题出现
在使用Spring security框架进行登录权限控制时，无法跳转正确的页面，一直跳转错误界面，查看debug日志信息发现报错：

```
###org.springframework.security.authentication.BadCredentialsException: Bad credentials
```
### 2、原因：
spring security 存储密码时会对密码进行加密，获取密码时会对密码进行解密，因为我的密码是自己手动输入到数据库的所以未经加密在spring security解析密码时就会产生错误，
### 3、解决方法
对数据库取出的密码进行BCrypt加密,代码如下：
new BCryptPasswordEncoder().encode(你从数据库取出的密码)

示例：

```
List roles=userInfo.getRoles();
List authorities=getAuthority(roles);
User user=new User(userInfo.getUsername(),new BCryptPasswordEncoder().encode(userInfo.getPassword()),
    userInfo.getStatus()==0?false:true,true,true,true,authorities);
return user;
```
