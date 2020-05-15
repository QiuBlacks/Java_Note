# **spring基于注解的 AOP 配置（不使用xml）**

**在使用xml的基础上，用以下来代替bean.xml**

```
@Configuration //指定当前类是一个spring配置类
@ComponentScan(basePackages="com.itheima") // 配置spring创建容器时要扫描的包
@EnableAspectJAutoProxy //配置spring开启注解AOP的支持
public class SpringConfiguration { }
```

**一般配置AOP的代码为公共代码，例如工具类**