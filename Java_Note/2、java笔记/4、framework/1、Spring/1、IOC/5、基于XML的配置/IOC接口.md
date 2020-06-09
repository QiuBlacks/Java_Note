# 核心容器IOC接口
## 一、ApplicationContext（BeanFactory的子接口,适用于单例对象，经常使用此接口）
在构建核心容器时，创建对象采取的策略是立即加载的方式。也就是说，只要一读取完配置文件马上创建配置文件中配置的对象
### 1、三个常用实现类
**ClassPathXmlApplicationContext(最常用)**:可以加载类路径下的配置文件，要求配置文件必须在类路径下   <br/>
**FileSystemXmlApplicationContext**：可以加载磁盘任意路径下的配置文件(必须有访问权限) <br/>
**AnnotationConfigApplicationContext**：当我们使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解
```
ApplicationContext ac=new ClassPathXmlApplicationContext("bean.xml");
 
ApplicationContext ac=new FileSystemXmlApplicationContext("E:\\black user\\IDEA\\IDEA-Worksapce\\springtext\\src\\main\\resources\\bean.xml ");
      
```
## 二、BeanFactory(顶层接口，适用于多例对象)

在构建核心容器时，创建对象采取的策略是延迟加载的方式。什么时候根据id获取对象就什么时候创建对象
```
    Resource resource=new ClassPathResource("bean.xml");
    BeanFactory factory=new XmlBeanFactory(resource);
    IAccountService as=(IAccountService)factory.getBean("accountService");
 ```   

