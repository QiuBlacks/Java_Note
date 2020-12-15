# bean

# 一、bean标签
## 1、作用：
1）可以读取class反射创建一个对象，存入Spring容器中，然后可以通过id使用该对象

2）用于配置对象让 spring 来创建的

默认情况下它调用的是类中的**无参构造函数**。如果没有无参构造函数则不能创建成功。



## 2、属性：

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



# 二、bean的作用域

## 1、作用范围取值：
- singleton:单例的（默认值）

- singleton：多例的

- request:作用与web应用的请求范围

- session:作用与web应用的会话范围

- global-session:作用与集群环境的会话范围（全局范围）,当不是集群环境时，为session

  

## 2、单例对象：scope="singleton"

### 2.1 基本介绍

一个应用只有一个对象的实例。它的作用范围就是整个引用

生命周期（跟容器相同)：
1. 对象出生：当应用加载，创建容器时，对象就被创建了

2. 对象活着：只要容器在，对象一直活着。

3. 对象死亡：当应用卸载，销毁容器时，对象就被销毁了。


```
<bean id="InstanceFactory" class="com.itheima.factory.InstanceFactory" scope="singleton"></bean>
```


### 2.2 线程不安全

Spring框架中的单例bean不是线程安全的。

**spring 中的 bean 默认是单例模式**，spring 框架并没有对单例 bean 进行多线程的封装处理。

实际上大部分时候 spring bean 无状态的（比如 dao 类），所以某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean()了，所以就可以保证线程安全了。

- 有状态就是有数据存储功能。
- 无状态就是不会保存数据。





## 3、多例对象：scope="prototype"

```
每次访问对象时，都会重新创建对象实例

生命周期：
对象出生：当使用对象时，创建新的对象实例。
对象活着：只要对象在使用中，就一直活着。
对象死亡：当对象长时间不用时，被 java 的垃圾回收器回收了

<bean id="InstanceFactory" class="com.itheima.factory.InstanceFactory" scope="prototype"></bean>
```



# 三、生命周期

bean在Spring容器中从创建到销毁经历了若干阶段，每一阶段都可以针对Spring如何管理bean进行个性化定制。

正如你所见，在bean准备就绪之前，bean工厂执行了若干启动步骤。

![在这里插入图片描述](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200928164140.png)

我们对上图进行详细描述：

1. Spring对bean进行实例化；

2. Spring将值和bean的引用依赖注入到bean对应的属性中；

3. 如果bean实现了BeanNameAware接口，Spring将bean的ID传递给setBean-Name()方法；

4. 如果bean实现了BeanFactoryAware接口，Spring将调用setBeanFactory()方法，将BeanFactory容器实例传入；

5. 如果bean实现了ApplicationContextAware接口，Spring将调用setApplicationContext()方法，将bean所在的应用上下文的引用传入进来；

6. 如果 BeanPostProcessor 和 Bean 关联，则 Spring 将调用该接口的预初始化方法 postProcessBeforeInitialzation() 对 Bean 进行加工操作，此处非常重要，Spring 的 **AOP 就是利用它实现的**。

7. 如果bean实现了InitializingBean接口，Spring将调用它们的after-PropertiesSet()方法。类似地，如果bean使用initmethod声明了初始化方法，该方法也会被调用；

8. 如果bean实现了BeanPostProcessor接口，Spring将调用它们的post-ProcessAfterInitialization()方法；

9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直**驻留在应用上下文中，直到该应用上下文被销毁**；

10. 如果bean实现了DisposableBean接口，Spring将调用它的destroy()接口方法。同样，如果bean使用destroy-method声明了销毁方法，该方法也会被调用。

    

Spring 为 Bean 提供了细致全面的生命周期过程，通过实现特定的接口或 <bean> 的属性设置，都可以对 Bean 的生命周期过程产生影响。虽然可以随意配置 <bean> 的属性，但是建议不要过多地使用 Bean 实现接口，因为这样会导致代码和 Spring 的聚合过于紧密





# 四、实例化bean的三种方式

## 1、默认无参构造函数

   在默认情况下：它会根据默认无参构造函数来创建类对象。如果 bean 中没有默认无参构造函数，将会创建失败。
```xml
在 Bean 对应的类里构造默认无参函数
<bean id="accountService" class="com.itheima.service.impl.AccountServiceImpl"></bean>-->
```
###     2、使用实例工厂的方法创建对象(使用某个类中的方法创建对象，并入Spring容器)
通过factory-bean找到 id 为 InstanceFactory类的factory-method方法
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

###  3、使用静态工厂的方法创建对象(使用某个类中的静态方法创建对象，并存入Spring容器)     
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