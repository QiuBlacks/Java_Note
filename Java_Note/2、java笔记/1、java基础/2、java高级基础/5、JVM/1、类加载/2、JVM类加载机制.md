# JVM类加载机制

# 一、基本概念
## 1、全盘负责
当一个类加载器负责加载某个Class时，该Class所依赖的和引用的其他Class也将由该类加载器负责载入，除非显示使用另外一个类加载器来载入
## 2、父类委托
先让父类加载器试图加载该类，只有在父类加载器无法加载该类时才尝试从自己的类路径中加载该类
## 3、缓存机制
缓存机制将会保证所有加载过的Class都会被缓存，当程序中需要使用某个Class时，类加载器先从缓存区寻找该Class，只有缓存区不存在，系统才会读取该类对应的二进制数据，并将其转换成Class对象，存入缓存区。这就是为什么==修改了Class后，必须重启JVM==，程序的修改才会生效
## 4、双亲委派机制（类加载器的代理模式）
如果一个类加载器收到了类加载的请求，它首先不会自己去尝试加载这个类，而是把请求委托给父加载器去完成，依次向上，因此，所有的类加载请求最终都应该被传递到顶层的启动类加载器中，只有当父加载器在它的搜索范围中没有找到所需的类时，即无法完成该加载，子加载器才会尝试自己去加载该类。
### 4.1   双亲委派机制
- 1、当AppClassLoader加载一个class时，它首先不会自己去尝试加载这个类，而是把类加载请求委派给父类加载器ExtClassLoader去完成。
- 2、当ExtClassLoader加载一个class时，它首先也不会自己去尝试加载这个类，而是把类加载请求委派给BootStrapClassLoader去完成。
- 3、如果BootStrapClassLoader加载失败(例如在$JAVA_HOME/jre/lib里未查找到该class)，会使用ExtClassLoader来尝试加载；
- 4、若ExtClassLoader也加载失败，则会使用AppClassLoader来加载，如果AppClassLoader也加载失败，则会报出异常==ClassNotFoundException==。
### 4.2   图解
![image](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165251.png)

### 4.3   代码实现

```java
public Class<?> loadClass(String name)throws ClassNotFoundException {
            return loadClass(name, false);
    }
protected synchronized Class<?> loadClass(String name, boolean resolve)throws ClassNotFoundException {
    // 首先判断该类型是否已经被加载
    Class c = findLoadedClass(name);
    if (c == null) {
        //如果没有被加载，就委托给父类加载或者委派给启动类加载器加载
        try {
            if (parent != null) {
                //如果存在父类加载器，就委派给父类加载器加载
                c = parent.loadClass(name, false);
            } else {
 //如果不存在父类加载器，就检查是否是由启动类加载器加载的类，通过调用本地方法native Class findBootstrapClass(String name)
                c = findBootstrapClass0(name);
            }
        } catch (ClassNotFoundException e) {
            // 如果父类加载器和启动类加载器都不能完成加载任务，才调用自身的加载功能
            c = findClass(name);
        }
    }
    if (resolve) {
        resolveClass(c);
    }
    return c;
}
```
### 4.4  双亲委派优势

- 系统类防止内存中出现多份同样的字节码

- 保证Java程序安全稳定运行


### 4.5  作用

- **防止重复加载同一个.class**。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。
- **保证核心.class不能被篡改**。通过委托方式，不会去篡改核心.clas，即使篡改也不会去加载，即使加载也不会是同一个.class对象了。不同的加载器加载同一个.class也不是同一个Class对象。这样保证了Class执行安全。





# 二、破坏双亲委派模型 

##  第一次“破坏” 

 双亲委派机制是在jdk1.2时出现的，因此在jdk1.2之前不满足双亲委派机制，即java.lang.ClassLoader抽象类，用户编写子类，然后能够重写loadClass()方法，这样就破坏了双亲委派机制； 

 解决：在ClassLoader中添加了protected的findClass()方法，引导用户重写loadClass()方法时，尽可能重新给这个方法，而不是在loadClass()方法中进行修改。 

##  第二次“破坏” 

 缺陷：越基础的类由越上层的类加载器进行加载，但是越基础的类型又需要去调用用户的代码，用户的代码一般由系统类加载器进行加载，这样引导类加载器无法调。 

 解决：线程上下文类加载器，启动类加载器想要调用我们的用户代码时，首先委托给线程上下文类加载器，然后线程上下文类加载器再去调用用户的程序代码。





## 参考文章

[【JVM笔记】如何打破双亲委派机制？](https://blog.csdn.net/cy973071263/article/details/104129163)















