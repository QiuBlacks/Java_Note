[TOC]



# **Spring 自动装配 Bean**

## **一、基本介绍**

**当Spring装配Bean属性时，有时候非常明确，就是需要将某个Bean的引用装配给指定属性。比如，如果我们的应用上下文中只有一个org.mybatis.spring.SqlSessionFactoryBean类型的Bean，那么任意一个依赖SqlSessionFactoryBean的其他Bean就是需要这个Bean。毕竟这里只有一个SqlSessionFactoryBean的Bean**

## **二、5 种自动装配模式**

两个类：

```java
public class Customer {   
    private Person person;   
    public void  setPerson { 
        this.Person =person
        }; 
}

 public class person{  
 		}
```

### **1、no** 

– 缺省情况下，自动配置是通过“ref”属性手动设定

```xml
<bean id="customer" class="com.lei.common.Customer">
    <property name="person" ref="person" />
</bean>

<bean id="person" class="com.lei.common.Person" />
```

### **2、byName** 

 会自动在容器上下文查找，和自己对象的==set方法==后面的值相同的beanId的bean

```xml
<bean id="customer" class="com.lei.common.Customer" autowire="byName" /> 

<bean id="person" class="com.lei.common.Person" />
```

**注意**：必须保证所有bean的Id唯一

### **3、byType** 

– 会自动在容器上下文查找，和自己对象的 **属性类型**相同的bean

```xml
<bean id="customer" class="com.lei.common.Customer" autowire="byType" />

<bean id="person" class="com.lei.common.Person" /> 
<bean id="person1" class="com.lei.common.Person" />
```

**注意：**确保你的配置文件中所有bean的==class唯一==

有两种相同数据类型的bean被配置，将抛出UnsatisfiedDependencyException异常，

### **4、constructor** 

-根据构造函数参数的数据类型，进行byType模式的自动装配。

### **5、autodetect** 

– 如果找到默认的构造函数，使用 “**constructor** ” 模式 ; 否则，使用“byType”模式。