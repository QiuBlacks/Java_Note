# spring独立测试

## 1、配置spring配置文件applicationContext.xml

注意：这里我们要求spring配置文件只扫描service和dao层的注解，而不去扫controller层

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
   http://www.springframework.org/schema/beans/spring-beans.xsd
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   http://www.springframework.org/schema/aop
   http://www.springframework.org/schema/aop/spring-aop.xsd
   http://www.springframework.org/schema/tx
   http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--开启注解的扫描，希望处理service和dao，controller不需要Spring框架去处理-->
    <context:component-scan base-package="com.qiu">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
    
</beans>
```



## 2、使用注解配置业务层和持久层

```java
@Repository("accountDao"）
public class AccountDaoImpl  implements IAccountDao {
    @Override
    public List<Account> findAll() {
        System.out.println("持久层：查询了所有账户");
        return null;
    }
    @Override
    public void saveAccount(Account account) {
        System.out.println("保存了账户");
    }
}
```

不调用持久层方法，只测试业务层AccountServiceImpl方法

```java
@Service("accountService")
public class AccountServiceImpl implements IAccountService {
    //@Autowired
   // private IAccountDao accountDao;
    public List<Account> findAll() {
        System.out.println("业务层：查询所有账户...");
        return null;
        //return accountDao.findAll();
    }
    public void saveAccount(Account account) {
        System.out.println("业务层：保存帐户...");
        //accountDao.saveAccount(account);
    }
}
```



## 3、测试

```java
public class Test01Spring {
    public static void main(String[] args) {
    ApplicationContext  ac  =  new  ClassPathXmlApplicationContext("applicationContext.xml");
    IAccountService as = ac.getBean("accountService",IAccountService.class);
    as.findAllAccount();
    }
}
```

