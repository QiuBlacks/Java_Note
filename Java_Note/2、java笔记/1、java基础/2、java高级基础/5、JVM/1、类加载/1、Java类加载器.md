[TOC]



#  Java类加载器

# 一、基本概念
## 1、类加载器（class loader）

顾名思义，类加载器（class loader）用来==加载 Java 类到 Java 虚拟机中==。
		一般来说，Java 虚拟机使用 Java 类的方式如下：
		Java 源程序（.java 文件）在经过 Java 编译器编译之后就被转换成 Java 字节代码（.class 文件）。类加载器负责读取 Java 字节代码，并转换成 java.lang.Class类的一个实例。每个这样的实例用来表示一个 Java 类。通过此实例的 newInstance()方法就可以创建出该类的一个对象。实际的情况可能更加复杂，比如 Java 字节代码可能是通过工具动态生成的，也可能是通过网络下载的。

在 Java 虚拟机判断两个类是否相同的时候，使用的是类的定义加载器。



## 2、类加载的过程

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165237.png" alt="image-20200730152651646" style="zoom:67%;" />	
		类加载的过程主要分为三个阶段：**加载、链接、初始化**

在这五个阶段中，加载、验证、准备和初始化这四个阶段发生的顺序是确定的，而解析阶段则不一定，它在某些情况下可以在初始化阶段之后开始，这是为了支持Java语言的运行时绑定(也成为动态绑定或晚期绑定)

### 2.1 加载

将class字节码文件加载到内存中，并将这些数据转换成方法区中的运行时数据（静态变量、静态代码块、常量池等），在堆中生成一个Class类对象代表这个类（反射原理），作为方法区类数据的访问入口。

### 2.2 链接

​	将Java类的二进制代码合并到JVM的运行状态之中。

 **• 验证**
 			确保加载的类信息符合JVM规范，没有安全方面的问题。

 **• 准备**
​			 为类的static变量分配内存并设置类变量初始值的阶段，这些内存都将在方法区中进行分配。注意此时的设置初始值为默认值，具体赋值在初始化阶段完成。

 **• 解析**
​		 虚拟机常量池内的符号引用替换为直接引用（地址引用）的过程。

```java
//符号引用：
		即一个字符串，但是这个字符串给出了一些能够唯一性识别一个方法，一个变量，一个类的相关信息。
//直接引用：
       可以理解为一个内存地址，或者一个偏移量。比如类方法，类变量的直接引用是指向方法区的指针；
//在解析阶段，虚拟机会把所有的类名，方法名，字段名这些符号引用替换为具体的内存地址或偏移量，也就是直接引用。
//举个例子来说：
       现在调用方法hello()，这个方法的地址是1234567，那么hello就是符号引用，1234567就是直接引用。
```



### 2.3  初始化

#### 1）JVM负责主要对类变量(类变量就是static修改的变量)进行初始化

```
1.声明静态类变量时指定初始值
2.使用静态代码块为类变量指定初始值
```

#### 2）类初始化时机：

只有当对类的主动使用的时候才会导致类的初始化，类的主动使用包括以下六种：

```
– 创建类的实例，也就是new的方式
– 访问某个类或接口的静态变量，或者对该静态变量赋值
– 调用类的静态方法
– 反射（如Class.forName(“com.shengsiyuan.Test”)）
– 初始化某个类的子类，则其父类也会被初始化
– Java虚拟机启动时被标明为启动类的类（Java Test），直接使用java.exe命令来运行某个主类
```

#### 3）Java程序初始化顺序

```json
父类的静态变量==》父类的静态代码块==》子类的静态变量==》子类的静态代码块==》父类的非静态变量==》
父类的非静态代码块==》父类的构造方法==》子类的非静态变量==》子类的非静态代码块==》子类的构造方法
```





# 二、类加载器的树状组织结构

Java 中的类加载器大致可以分成两类：

​			**一类是系统提供的，另外一类则是由 Java 应用开发人员编写的。**

系统提供的类加载器主要有下面三个：

### 1、引导（启动）类加载器（bootstrap class loader）

它用来加载 Java 的核心库，是用原生代码来实现的，并不继承自 java.lang.ClassLoader。。

### 2、扩展类加载器（extensions class loader）

它用来加载 Java 的扩展库。Java 虚拟机的实现会提供一个扩展库目录。该类加载器在此目录里面查找并加载 Java 类。。

### 3、系统类加载器（system class loader）

它根据 Java 应用的类路径（CLASSPATH）来加载 Java 类。一般来说，Java 应用的类都是由它来完成加载的。可以通过 ClassLoader.getSystemClassLoader()来获取它。



除了引导类加载器之外，所有的类加载器都有一个父类加载器。通过 getParent()方法可以得到。对于系统提供的类加载器来说，系统类加载器的父类加载器是扩展类加载器，而扩展类加载器的父类加载器是引导类加载器；

对于开发人员编写的类加载器来说，其父类加载器是加载此类加载器 Java 类的类加载器。因为类加载器 Java 类如同其它的 Java 类一样，也是要由类加载器来加载的。一般来说，开发人员编写的类加载器的父类加载器是系统类加载器。类加载器通过这种方式组织起来，形成树状结构。树的根节点就是引导类加载器。
<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165238.png" alt="image-20200611091943943" style="zoom:80%;" />

图 中给出了一个典型的类加载器树状组织结构示意图，其中的箭头指向的是父类加载器。





# 三、加载类的方法

## 1、Class.forName（）动态加载
Class.forName是一个静态方法，同样可以用来加载类。
该方法有两种形式：

```java
Class.forName(String name, boolean initialize, ClassLoader loader)
```
第一种形式的参数 name表示的是类的全名；initialize表示是否初始化类；loader表示加载时使用的类加载器。java



```java
Class.forName(String className)。
```

第二种形式则相当于设置了参数 initialize的值为 true，loader的值为当前类的类加载器。

## 2、ClassLoader.loadClass()方法动态加载

## 3、命令行启动应用时候由JVM初始化加载

## 4、代码测试
```java
package com.pdai.jvm.classloader;
public class loaderTest { 
        public static void main(String[] args) throws ClassNotFoundException { 
                ClassLoader loader = HelloWorld.class.getClassLoader(); 
                System.out.println(loader); 
                //使用ClassLoader.loadClass()来加载类，不会执行初始化块 
                loader.loadClass("Test2"); 
                //使用Class.forName()来加载类，默认会执行初始化块 
//                Class.forName("Test2"); 
                //使用Class.forName()来加载类，并指定ClassLoader，初始化时不执行静态块 
//                Class.forName("Test2", false, loader); 
        } 
}

public class Test2 { 
        static { 
                System.out.println("静态初始化块执行了！"); 
        } 
}
```







# 四、类加载器知识点

## 1、Java 虚拟机是如何判定两个 Java 类是相同的：

Java 虚拟机不仅要看**类的全名**是否相同，还要看**加载此类的类加载器**是否一样。只有两者都相同的情况，才认为两个类是相同的。

即便是同样的字节代码，被不同的类加载器加载之后所得到的类，也是不同的。比如一个 Java 类 com.example.Sample，编译之后生成了字节代码文件 Sample.class。两个不同的类加载器 ClassLoaderA和 ClassLoaderB分别读取了这个 Sample.class文件，并定义出两个 java.lang.Class类的实例来表示这个类。这两个实例是不相同的。对于 Java 虚拟机来说，它们是不同的类。试图对这两个类的对象进行相互赋值，会抛出运行时异常 ClassCastException。



## 2、类的定义加载器和初始加载器

在前面介绍类加载器的代理模式的时候，提到过类加载器会首先代理给其它类加载器来尝试加载某个类。这就意味着：==真正完成类的加载工作的类加载器和启动这个加载过程的类加载器，有可能不是同一个==。真正完成类的加载工作是通过调用 defineClass来实现的；而启动类的加载过程是通过调用 loadClass来实现的。前者称为一个**类的定义加载器（defining loader）**，后者称为**初始加载器（initiating loader）。**

在 Java 虚拟机判断两个类是否相同的时候，使用的是类的定义加载器。也就是说，哪个类加载器启动类的加载过程并不重要，重要的是最终定义这个类的加载器。两种类加载器的关联之处在于：一个类的定义加载器是它引用的其它类的初始加载器。如类 com.example.Outer引用了类 com.example.Inner，则由类 com.example.Outer的定义加载器负责启动类 com.example.Inner的加载过程。

方法 loadClass()抛出的是 ClassNotFoundException异常；方法 defineClass()抛出的是NoClassDefFoundError异常。
		类加载器在成功加载某个类之后，会把得到的 java.lang.Class类的实例缓存起来。下次再请求加载该类的时候，类加载器会直接使用缓存的类的实例，而不会尝试再次加载。也就是说，对于一个类加载器实例来说，相同全名的类只加载一次，即 loadClass方法不会被重复调用。



## 3、Class.forName()和ClassLoader.loadClass()区别：

- Class.forName(): 将类的.class文件加载到jvm中之外，还会对类进行解释，执行类中的static块；
- ClassLoader.loadClass(): 只干一件事情，就是将.class文件加载到jvm中，不会执行static中的内容,只有在newInstance才会去执行static块。 
- Class.forName(name, initialize, loader)带参函数也可控制是否加载static块。并且只有调用了newInstance()方法采用调用构造函数，创建类的对象 。





## 参考文章

[【Java基础】类加载过程](https://www.jianshu.com/p/dd39654231e0)

[JVM 基础 - Java 类加载机制](https://www.pdai.tech/md/java/jvm/java-jvm-classload.html)

[java类的加载过程](https://blog.csdn.net/weixin_43689480/article/details/96098639)



