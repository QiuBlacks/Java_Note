[TOC]

# Spring 自动装配 Bean

# 一、基本介绍

## 1、基础知识

Spring提供了三种主要的装配机制：

- 在XMl中进行显示配置
- 在Java中进行显示配置
- 隐式的bean发现机制和自动装配
  - 组件扫描（component scanning）：Spring会自动发现应用上下文中所创建的bean。
  - 自动装配（autowiring）：Spring自动满足bean之间的依赖。

使用的优先性: 3>2>1  。尽可能地使用自动配置的机制，显示配置越少越好。当必须使用显示配置bean的时候（如：有些源码不是由你来维护的，而当你需要为这些代码配置bean的时候），推荐使用类型安全比XML更加强大的JavaConfig。最后只有当你想要使用便利的XML命名空间，并且在JavaConfig中没有同样的实现时，才使用XML。



## 2、自动装配Bean

当Spring装配Bean属性时，有时候非常明确，就是需要将某个Bean的引用装配给指定属性。

比如，如果我们的应用上下文中只有一个org.mybatis.spring.SqlSessionFactoryBean类型的Bean，那么任意一个依赖SqlSessionFactoryBean的其他Bean就是需要这个Bean。毕竟这里只有一个SqlSessionFactoryBean的Bean



# 二、5 种自动装配模式

两个类：

```java
public class Customer {   
    private Person person;   
    public void  setPerson { 
        this.Person =person; 
        }
}

public class person{  
 		}
```

## 1、no 

– 缺省情况下，自动配置是通过“ref”属性手动设定

```xml
<bean id="customer" class="com.lei.common.Customer">
    <property name="person" ref="person" />
</bean>
<bean id="person" class="com.lei.common.Person" />
```

## 2、byName 

 会自动在容器上下文查找，和自己对象的==set方法==后面的值相同的beanId的bean

```xml
<bean id="customer" class="com.lei.common.Customer" autowire="byName" /> 
<bean id="person" class="com.lei.common.Person" />
```

注意：必须保证所有bean的Id唯一

## 3、byType 

– 会自动在容器上下文查找，和自己对象的 属性类型相同的bean

```xml
<bean id="customer" class="com.lei.common.Customer" autowire="byType" />

<bean id="person" class="com.lei.common.Person" /> 
<bean id="person1" class="com.lei.common.Person" />  //报错
```

注意：确保你的配置文件中所有bean的==class唯一==

有两种相同数据类型的bean被配置，将抛出UnsatisfiedDependencyException异常，

## 4、constructor 

-根据构造函数参数的数据类型，进行byType模式的自动装配。



## 5、autodetect 

– 如果找到默认的构造函数，使用 “constructor ” 模式 ; 否则，使用“byType”模式。