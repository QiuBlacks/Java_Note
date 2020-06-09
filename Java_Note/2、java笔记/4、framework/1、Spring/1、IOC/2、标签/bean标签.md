[TOC]



# bean

## 一、bean标签
### 1、作用：
1）可以读取class反射创建一个对象，存入Spring容器中，然后可以通过id使用该对象

2）用于配置对象让 spring 来创建的。
默认情况下它调用的是类中的无参构造函数。如果没有无参构造函数则不能创建成功。

### 2、属性：
```javascript
id：给对象在容器中提供一个唯一标识。用于获取对象。
class：指定类的全限定类名。用于反射创建对象。默认情况下调用无参构造函数。
scope：指定对象的作用范围。
    * singleton :默认值，单例的.
    * prototype :多例的.
    * request  :WEB 项目中,Spring 创建一个 Bean 的对象,将对象存入到 request 域中.
    * session  :WEB 项目中,Spring 创建一个 Bean 的对象,将对象存入到 session 域中.
    * global session  :WEB 项目中,应用在 Portlet 环境.如果没有 Portlet 环境,那么globalSession 相当于 session.
init-method：指定类中的初始化方法名称。
destroy-method：指定类中销毁方法名称。
factory-bean:设置bean工厂，class属性必须为空，factory-bean属性必须指定一个bean的名字，这个bean一定要在当前的bean工厂或者父bean厂中
factory-method：工厂中的工厂方法

```



## 二、bean的作用范围和生命周期

### 作用范围取值：
```
singleton:单例的（默认值）
singleton：多例的
request:作用与web应用的请求范围
session:作用与web应用的会话范围
global-session:作用与集群环境的会话范围（全局范围）,当不是集群环境时，为session
```
### 单例对象：scope="singleton"
```
一个应用只有一个对象的实例。它的作用范围就是整个引用

生命周期（跟容器相同)：
1. 对象出生：当应用加载，创建容器时，对象就被创建了
2. 对象活着：只要容器在，对象一直活着。
3. 对象死亡：当应用卸载，销毁容器时，对象就被销毁了。

<bean id="InstanceFactory" class="com.itheima.factory.InstanceFactory" scope="singleton"></bean>
```
### 多例对象：scope="prototype"
```
每次访问对象时，都会重新创建对象实例

生命周期：
对象出生：当使用对象时，创建新的对象实例。
对象活着：只要对象在使用中，就一直活着。
对象死亡：当对象长时间不用时，被 java 的垃圾回收器回收了

<bean id="InstanceFactory" class="com.itheima.factory.InstanceFactory" scope="prototype"></bean>
```



## 三、实例化bean的三种方式

###   第一种方式：使用默认无参构造函数
   在默认情况下：它会根据默认无参构造函数来创建类对象。如果 bean 中没有默认无参构造函数，将会创建失败。
```xml
 1、在 Bean 对应的类里构造默认无参函数
2、<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>-->
```
###     第二种方式：使用实例工厂的方法创建对象(使用某个类中的方法创建对象，并入Spring容器)
通过factory-bean找到id InstanceFactory类的factory-method方法
  ```  xml
/**
* 模拟一个实例工厂，创建业务层实现类
* 此工厂创建对象，必须现有工厂实例对象，再调用方法
*/
public class InstanceFactory {
    public IAccountService createAccountService(){
        return new AccountServiceImpl();
}
}
<!-- 此种方式是：
先把工厂的创建交给 spring 来管理。
然后在使用工厂的 bean 来调用里面的方法
factory-bean 属性：用于指定实例工厂 bean 的 id。
factory-method 属性：用于指定实例工厂中创建对象的方法。
-->
<bean id="instancFactory" class="com.itheima.factory.InstanceFactory"></bean>
<bean id="accountService" factory-bean="instancFactory" factory-method="createAccountService"></bean>
  ```

###  第三种方式：使用静态工厂的方法创建对象(使用某个类中的静态方法创建对象，并存入Spring容器)     
```xml
/**
* 模拟一个静态工厂，创建业务层实现类
*/
public class StaticFactory { 
    public static IAccountService createAccountService(){
        return new AccountServiceImpl();
}
}
<!-- 此种方式是:
使用 StaticFactory 类中的静态方法 createAccountService 创建对象，并存入 spring 容器
id 属性：指定 bean 的 id，用于从容器中获取
class 属性：指定静态工厂的全限定类名
factory-method 属性：指定生产对象的静态方法
-->
<bean id="accountService"  class="com.itheima.factory.StaticFactory"  
      factory-method="createAccountService"></bean>
````