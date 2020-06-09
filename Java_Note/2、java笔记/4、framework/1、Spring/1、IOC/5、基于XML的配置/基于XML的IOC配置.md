# 使用spring的 IOC解决程序耦合
IOC作用：削减计算机程序的耦合(解除我们代码中的依赖关系)。
## 一、准备工作：
### 1、下载jar包：
http://repo.springsource.org/libs-release-local/org/springframework/spring
### 2、基于XML的配置
#### 第一步：创建依赖    或者   拷贝必备的 jar  包到工程的 lib                
```xml
    或者在总Poml.xml中配置
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.1.9.RELEASE</version>
        </dependency>
    </dependencies>
```

#### 第二步：在类的根路径下创建的 一个任意名称的xml  文件（不能是中文）,配置以下  (如果报错，就重启项目)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```
#### 第三步让spring管理资源配置，在配置文件中配置 service和 dao
```xml
<!-- bean 标签：用于配置让 spring 创建对象，并且存入 ioc 容器之中
id 属性：对象的唯一标识。
class 属性：指定要创建对象的全限定类名
-->
<!-- 配置 service -->
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl">
</bean>
<!-- 配置 dao -->
<bean id="accountDao" class="com.itheima.dao.impl.AccountDaoImpl"></bean>
```
#### 第四步、测试配置是否成功
```      //1、获取核心容器对象
        ApplicationContext ac=new ClassPathXmlApplicationContext("bean.xml");
        //2、根据Id获取Bean对象
        IAccountService as=(IAccountService)ac.getBean("accountService");
        IAccountDao adao=(IAccountDao)ac.getBean("accountDao",IAccountDao.class);
```

