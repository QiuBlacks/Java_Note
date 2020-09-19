[TOC]



## <aop:aspect >与<aop:advisor >

## 一、基本介绍

< aop:aspect>：定义切面（切面包括通知和切点）

< aop:advisor>：定义通知器（通知器跟切面一样，也包括通知和切点）



< aop:advisor>和< aop:aspect>其实都是将通知和切面进行了封装，原理基本上是一样的，只是使用的方式不同而已。



## 二、区别

### 1、实现方式不同

< aop:aspect>定义切面时，只需要定义一般的bean就行

而定义< aop:advisor>中引用的通知时，通知必须实现==Advice接口==



### 2、使用场景不同

< aop:advisor>大多用于事务管理

< aop:aspect>大多用于日志，缓存



## 三、实例

### 1、定义一个接口Sleepable和这个接口的实现Human

```java
public interface Sleepable {
    public void sleep();
}

public class Human implements Sleepable {

    @Override
    public void sleep() {
        System.out.println("我要睡觉了！");
    }
}
```

### 2、< aop:advisor>的实现方式：

```java
//定义通知:实现advice接口
public class SleepHelper implements MethodBeforeAdvice,AfterReturningAdvice{
    @Override
    public void before(Method arg0, Object[] arg1, Object arg2)
            throws Throwable {
        System.out.println("睡觉前要脱衣服！");
    }

    @Override
    public void afterReturning(Object arg0, Method arg1, Object[] arg2,
                               Object arg3) throws Throwable {
        System.out.println("起床后要穿衣服！");
    }
}
```

aop配置

```xml
<bean id="sleepHelper" class="com.ghs.aop.SleepHelper"></bean>

<aop:config>
    <aop:pointcut expression="execution(* *.sleep(..))" id="sleepPointcut"/>
    <aop:advisor advice-ref="sleepHelper" pointcut-ref="sleepPointcut"/>
</aop:config>

<bean id="human" class="com.ghs.aop.Human"/>
```



### 3、< aop:aspect>的实现方式：

```java
//定义切面：不用实现接口
public class SleepHelperAspect{
    public void beforeSleep(){
        System.out.println("睡觉前要脱衣服！");
    }

    public void afterSleep(){
        System.out.println("起床后要穿衣服！");
    }
}
```

aop配置

```xml
<bean id="sleepHelperAspect" class="com.ghs.aop.SleepHelperAspect"></bean>

<aop:config>
    <aop:pointcut expression="execution(* *.sleep(..))" id="sleepPointcut"/>
        <aop:aspect ref="sleepHelperAspect">
        <!--前置通知-->
        <aop:before method="beforeSleep" pointcut-ref="sleepPointcut"/>
        <!--后置通知-->
        <aop:after method="afterSleep" pointcut-ref="sleepPointcut"/>
    </aop:aspect>
</aop:config>

<bean id="human" class="com.ghs.aop.Human"/>
```

