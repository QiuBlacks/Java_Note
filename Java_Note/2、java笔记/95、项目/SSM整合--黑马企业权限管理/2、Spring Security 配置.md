# Spring Security 配置

# 一、基本介绍



# 二、配置

依赖插件、监听器、过滤器

## 1、xml配置

```xml
<security:global-method-security pre-post-annotations="enabled" jsr250-annotations="enabled" secured-annotations="enabled"></security:global-method-security>

    <!-- 配置不拦截的资源 -->
    <security:http pattern="/login.jsp" security="none"/>
    <security:http pattern="/failer.jsp" security="none"/>
    <security:http pattern="/css/**" security="none"/>
    <security:http pattern="/img/**" security="none"/>
    <security:http pattern="/plugins/**" security="none"/>

    <!--配置具体的规则
    	auto-config="true"	不用自己编写登录的页面，框架提供默认登录页面
    	use-expressions="true"	开启SPEL表达式：spring security -->
    <security:http auto-config="true" use-expressions="true">
        
<!-- 配置具体的拦截的规则 pattern="请求路径的规则" access="访问系统的人，必须有ROLE_USER的角色" -->
        <security:intercept-url pattern="/**" access="hasAnyRole('ROLE_USER','ROLE_ADMIN')"/>

        <!-- 定义跳转的具体的页面 -->
        <security:form-login
                login-page="/login.jsp"
                login-processing-url="/login.do"
                default-target-url="/index.jsp"
                authentication-failure-url="/failer.jsp"
                authentication-success-forward-url="/pages/main.jsp" />

        <!-- 关闭跨域请求 -->
        <security:csrf disabled="true"/>
        <!-- 退出操作 -->
        <security:logout invalidate-session="true" logout-url="/logout.do" logout-success-url="/login.jsp" />

    </security:http>

    <!-- 切换成数据库中的用户名和密码 -->
    <security:authentication-manager>
        <security:authentication-provider user-service-ref="userService">
            <!-- 配置加密的方式-->
            <security:password-encoder ref="passwordEncoder"/>
        </security:authentication-provider>
    </security:authentication-manager>

    <!-- 配置加密类 -->
    <bean id="passwordEncoder" class="org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder"/>

    <!-- 提供了入门的方式，在内存中存入用户名和密码
    <security:authentication-manager>
    	<security:authentication-provider>
    		<security:user-service>
    			<security:user name="admin" password="{noop}admin" authorities="ROLE_USER"/>
    		</security:user-service>
    	</security:authentication-provider>
    </security:authentication-manager>
    -->

</beans>
```



## 2、认证

### 2.1 用户登陆认证

使用UserDetails、UserDetailsService来完成操作  

UserDetails是一个接口，我们可以认为UserDetails作用于封装当前进行认证的用户信息，但由于其是一个接口，所以我们可以对其进行实现，也可以使用Spring Security提供的一个UserDetails的实现类User来完成操作  

实现IUserService接口，重写loadUserByUsername方法，getAuthority（）

```java
@Service("userService")
@Transactional
public class UserServiceImpl implements IUserService {

    @Autowired
    private IUserDao userDao;

    @Autowired
    private BCryptPasswordEncoder bCryptPasswordEncoder;

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
        User user = new User(userInfo.getUsername(), userInfo.getPassword(), userInfo.getStatus() == 0 ? false : true, true, true, true, getAuthority(userInfo.getRoles()));

        return user;//new BCryptPasswordEncoder().encode(
    }

    //作用就是返回一个List集合，集合中装入的是角色描述
    public List<SimpleGrantedAuthority> getAuthority(List<Role> roles) {

        List<SimpleGrantedAuthority> list = new ArrayList<>();
        for (Role role : roles) {
            list.add(new SimpleGrantedAuthority("ROLE_" + role.getRoleName()));
        }
        return list;
    }
```



### 2.2  加密

在保存账户时，对密码进行加密

加密方式BCryptPasswordEncoder

```java
  
      @Override
    public void save(UserInfo userInfo) throws Exception {
        //对密码进行加密处理
        userInfo.setPassword(bCryptPasswordEncoder.encode(userInfo.getPassword()));
        userDao.save(userInfo);
    }
```





## 3、授权

### 3.1服务器端方法级权限控制  

 JSR-250注解、@Secured注解和支持表达式的注解  

#### 首先开启方法权限控制  

1）在Spring Security.xml中开启

```
<security:global-method-security jsr250-annotations="enabled"/>
<security:global-method-security secured-annotations="enabled"/>
<security:global-method-security pre-post-annotations="disabled"/>
```

2）注解开启

**@EnableGlobalMethodSecurity** ：Spring Security默认是禁用注解的，要想开启注解，需要在继承WebSecurityConfigurerAdapter的类上加@EnableGlobalMethodSecurity注解，并在该类中将AuthenticationManager定义为Bean  



#### 3.1.1 JSR-250注解  

@RolesAllowed表示访问对应方法时所应该具有的角色  

​		参数可以是ADMIN,USER

用在角色允许访问的方法上

```java
   @RolesAllowed("USER")
    @RequestMapping("/findAll.do")
    public ModelAndView findAll() throws Exception {
        ModelAndView mv = new ModelAndView();
        List<Product> ps = productService.findAll();
        mv.addObject("productList", ps);
        mv.setViewName("product-list");
        return mv;
    }
```



#### 3.1.2 @Secured注解  

与JSR-250注解  不同的是角色前面要加  ROLE_

```
@Secured("ROLE_ADMIN")
    @RequestMapping("/findAll.do")
    public ModelAndView findAll() throws Exception {
        ModelAndView mv = new ModelAndView();
        List<Product> ps = productService.findAll();
        mv.addObject("productList", ps);
        mv.setViewName("product-list");
        return mv;
    }
```



#### 3.1.3 支持Sqle表达式的注解  

##### 1) @PreAuthorize

 在方法调用之前,基于表达式的计算结果来限制对方法的访问
		示例：

```java
@PreAuthorize("#userId == authentication.principal.userId or hasAuthority(‘ADMIN’)")
// @PreAuthorize("authentication.principal.username = 'qiu'")
//@PreAuthorize("hasRole('ROLE_ADMIN')")
void changePassword(@P("userId") long userId ){ }

```

这里表示在changePassword方法执行之前，判断方法参数userId的值是否等于principal中保存的当前用户的userId，或者当前用户是否具有ROLE_ADMIN权限，两种符合其一，就可以访问该方法。

##### 2) @PostAuthorize 

允许方法调用,但是如果表达式计算结果为false,将抛出一个安全性异常
		示例：

```java
@PostAuthorize
User getUser("returnObject.userId == authentication.principal.userId or
hasPermission(returnObject, 'ADMIN')");
```



@PostFilter 允许方法调用,但必须按照表达式来过滤方法的结果
		@PreFilter 允许方法调用,但必须在进入方法之前过滤输入值  



### 3.2 页面端标签控制权限 

#### 3.2.1 准备

导入依赖 

```
<dependency>
	<groupId>org.springframework.security</groupId>
		<artifactId>spring-security-taglibs</artifactId>
	<version>version</version>
</dependency>
```

在使用的页面上加上	

```
<%@taglib uri="http://www.springframework.org/security/tags" prefix="security"%>
```



#### 3.2.2 常用标签

##### 1）authentication

```
<security:authentication property="" htmlEscape="" scope="" var=""/>
```

- property： 只允许指定Authentication所拥有的属性，可以进行属性的级联获取，如“principle.username”，
  不允许直接通过方法进行调用
- htmlEscape：表示是否需要将html进行转义。默认为true。
- scope：与var属性一起使用，用于指定存放获取的结果的属性名的作用范围，默认我pageContext。Jsp中拥
  有的作用范围都进行进行指定
- var： 用于指定一个属性名，这样当获取到了authentication的相关信息后会将其以var指定的属性名进行存
  放，默认是存放在pageConext中  





##### 2)authorize  

authorize是用来判断普通权限的，通过判断用户是否具有对应的权限而控制其所包含内容的显示  

```
<security:authorize access="" method="" url="" var=""></security:authorize>
```

- access： 需要使用表达式来判断权限，当表达式的返回结果为true时表示拥有对应的权限
- method：method属性是配合url属性一起使用的，表示用户应当具有指定url指定method访问的权限，method的默认值为GET，可选值为http请求的7种方法
- url：url表示如果用户拥有访问指定url的权限即表示可以显示authorize标签包含的内容
- var：用于指定将权限鉴定的结果存放在pageContext的哪个属性中  

示例：

用在**角色允许访问的页面模块**上

```xml
<li id="system-setting">
	<security:authorize access="hasRole('USER')">
		<a
			href="${pageContext.request.contextPath}/user/findAll.do"> 
			<i class="fa fa-circle-o"></i> 用户管理
		</a>
	</security:authorize>
 </li>
```



##### 3）accesscontrollist  

accesscontrollist标签是用于鉴定ACL权限的。其一共定义了三个属性：hasPermission、domainObject和var，其中前两个是必须指定的  

```
<security:accesscontrollist hasPermission="" domainObject="" var=""></security:accesscontrollist>
```

- hasPermission：hasPermission属性用于指定以逗号分隔的权限列表
- domainObject：domainObject用于指定对应的域对象
- var：var则是用以将鉴定的结果以指定的属性名存入pageContext中，以供同一页面的其它地方使用  