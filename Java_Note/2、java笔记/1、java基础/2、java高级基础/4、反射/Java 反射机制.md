[TOC]



# Java 反射机制

---

## 一、反射机制概述
Java 反射机制是在==运行状态==中，对于任意一个类，都能够获得这个类的所有属性和方法，对于任意一个对象都能够调用它的任意一个属性和方法。这种在运行时动态的获取信息以及动态调用对象的方法的功能称为 Java 的反射机制
![image](https://img-blog.csdnimg.cn/20191023152026144.png)



## 二、反射的作用
灵活的编写代码，代码可以在运行时装配，无需在组件之间进行源代码链接，降低代码的耦合度；还有动态代理的实现等等；但是需要注意的是反射使用不当会造成很高的资源消耗！<br>

普通方式创建的对象就不能再变了，我只能选择是运行还是不运行这个.class文件。是不是感觉很僵硬，假如现在我有个写好的程序已经放在了服务器上，每天供人家来访问，这时候Mysql数据库宕掉了，改用Oracle,这时候该怎么怎么办呢？假如没有反射的话，我们是不是得修改代码，将Mysql驱动改为Oracle驱动，重新编译运行，再放到服务器上。是不是很麻烦，还影响用户的访问。<br>

&emsp;&emsp;
		假如我们使用反射Class.forName()来加载驱动，只需要修改配置文件就可以动态加载这个类，Class.forName()生成的结果在编译时是不可知的，只有在运行的时候才能加载这个类，换句话说，此时我们是不需要将程序停下来，只需要修改配置文件里面的信息就可以了。这样当有用户在浏览器访问这个网站时，都不会感觉到服务器程序程序发生了变化。



## 三、反射机制的实现

步骤：

- 获取想要操作的类的Class对象，通过Class对象任意调用类的方法。
- 调用Class类中的方法。
- 通过反射API操作信息。

### 1、Class对象的获取
- 对象的getClass()方法

```Java
Person p1 = new Person();
  Class c1 = p1.getClass();
```


- 类.class  属性;		(最安全/性能最好)

```Java
Class c2 = Person.class;
```


- 运用Class.forName(String className)动态加载类

className需要是类的全限定名(最常用)

```Java
Class c3 = Class.forName("com.ys.reflex.Person");
```



### 2、创建对象

#### 通过反射来生成对象的方式有两种:<br>
1）使用Class对象的newInstance()方法来创建该Class对象对应类的实例	(这种方式要求该Class对象的对应类有默认构造器).

```Java
Class<User> c = User.class;
 User u = c.newInstance();
//（直接newInstance的话必须保证默认的构造方法正常存在，也就是没有被私有化！这是前提条件）
```

2）先使用Class对象获取指定的Constructor对象, 再调用Constructor对象的newInstance()方法来创建该Class对象对应类的实例(通过这种方式可以选择指定的构造器来创建实例).

```Java
Class<User> c = User.class;
//获取需要被调用的指定构造器(private 修饰的构造方法) 
//String.class，int.class,int.class为获取的构造器的构造参数
    Constructor<Per> con = c.getDeclaredConstructor(String.class，int.class，int.class);
 //实例化对象  
    Per p = con.newInstance();
```



### 3、通过反射机制获取类信息

```Java
	getName()：获得类的完整名字。
    getFields()：获得类的public类型的属性。
    getDeclaredFields()：获得类的所有属性。包括private 声明的和继承类
    getMethods()：获得类的public类型的方法。
    getDeclaredMethods()：获得类的所有方法。包括private 声明的和继承类
    getMethod(String name, Class[] parameterTypes)：获得类的特定方法，name参数指定方法的名字，parameterTypes 参数指定方法的参数类型。
    getConstructors()：获得类的public类型的构造方法。
    getConstructor(Class[] parameterTypes)：获得类的特定构造方法，parameterTypes 参数指定构造方法的参数类型。
    newInstance()：通过类的不带参数的构造方法创建这个类的一个对象。
```


#### 1）通过反射机制获取 Class 中的方法

```Java
User u3 = (User) clazz.newInstance();  
  Method method = clazz.getDeclaredMethod("setId", int.class);  
  method.invoke(u3, 1002);
```


#### 2) 通过反射操作普通的属性 

```Java
User u4 = (User) clazz.newInstance();  
    Field f = clazz.getDeclaredField("name");  
    f.setAccessible(true); // 设置属性可以直接的进行访问  
    f.set(u4, "石头");
```

