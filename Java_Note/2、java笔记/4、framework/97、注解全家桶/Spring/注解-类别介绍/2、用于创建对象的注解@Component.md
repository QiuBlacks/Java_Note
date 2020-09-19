# 用于创建对象的注解

# 一、@Component

作用：用于把当前类对象存入spring容器中

 属性：

​             value：用于指定bean的id。当我们不写时，它的默认值是当前类名，且首字母改小写。

## 1、配置bean.xml文件

```
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xmlns:context="http://www.springframework.org/schema/context"
           xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
        
<!--告知spring在创建容器时要扫描的包，配置所需要的标签不是在beans的约束中，而是一个名称为context名称空间和约束中-->
    <context:component-scan base-package="com.itheima"></context:component-scan>

</beans>
```

## 2、添加注解

@Component 默认vaule为类名，首字母小写，也可自定义

```
@Component（value=" accountService")
public class AccountServiceImpl implements IAccountService {

    private String name;
    private Integer age;
    private Date birthday;
}

   public static void main(String[] args) {
        //1.获取核心容器对象
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        //2.根据id获取Bean对象
        IAccountService as  = (IAccountService)ac.getBean("accountService ");
}   
```

​    

# 二、三层数据

以上三个注解他们的作用和属性与Component是一模一样

Controller：一般用在表现层

 Service：一般用在业务层

 Repository：一般用在持久层

​    他们三个是spring框架为我们提供明确的三层使用的注解，使我们的三层对象更加清晰