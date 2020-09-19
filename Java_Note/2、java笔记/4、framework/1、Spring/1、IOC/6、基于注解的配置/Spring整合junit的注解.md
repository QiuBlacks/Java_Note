# Spring整合junit的注解

#  一、Spring整合junit的配置步骤：

​     1、导入spring整合junit的jar包 (坐标)

```
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.0.2.RELEASE</version>
</dependency>

<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.12</version>
</dependency>
```

​     2、使用Junit提供的一个注解把原有的main方法替换了，替换成spring提供的

​           @Runwith

​    3、告知spring的运行器，spring和ioc创建是基于xml还是注解的，并且说明位置

​         @ContextConfiguration

```
locations：指定xml文件的位置，加上classpath关键字，表示在类路径下		@ContextConfiguration( locations= “classpath="bean.xml" )

 classes：指定注解类所在地位置    						@ContextConfiguration(classes = SpringConfiguration.class)
```

注意：  当我们使用spring 5.x版本的时候，要求junit的jar必须是4.12及以上

# 二、例子：

开发测试时，junit无法识别spring框架，不能使用容器

```java
@Test
public void testFindAll() {
    //1.获取容易
    ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
    //2.得到业务层对象
    IAccountService as = ac.getBean("accountService",IAccountService.class);
    //3.执行方法
    List<Account> accounts = as.findAllAccount();
    for(Account account : accounts){
        System.out.println(account);
    }
}
```



```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfiguration.class)
public class AccountServiceTest {
    @Autowired
    private IAccountService as = null;

    @Test
    public void testFindAll() {
        //3.执行方法
        List<Account> accounts = as.findAllAccount();
        for(Account account : accounts){
            System.out.println(account);
        }
    }
```

