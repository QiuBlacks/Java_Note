# java类型
---

# 一、java的数据类型
1. 基本类型：byte，short，int，long，boolean，float，double，char
1. 引用类型：所有class和interface类型


# 二、包装类型


基本类型 | byte | short | int| long| float|  double | char  |  boolean
---|---|---|---|---|:-:|---|---|---
包装类型| Byte | Short | Integer |Long| Float | Double |Character |Boolean
 位数	|	8 | 	16|	32|	64|	32	|64	|16|	1 


注意：包装类型初始值默认为null;

```
Integer a;   //a=null;
```

B


## 1、问题引出
 &emsp; &emsp;引用类型可以赋值为null，表示空，但基本类型不能赋值为null：如何把一个基本类型视为对象（引用类型）？
## 2、解决方法
 &emsp; &emsp;我们可以定义一个Integer类，它只包含一个实例字段int，这样，Integer类就可以视为int的包装类（Wrapper Class）

```
public class Integer {
    private int value;

    public Integer(int value) {
        this.value = value;
    }

    public int intValue() {
        return this.value;
    }
```

即可使用
Integer a=null;

# 三、深入剖析装箱和拆箱
--------------------------------------------------------------------------------
## 一、基本介绍
## 1、定义
- 装箱：将基本数据类型转化为包装类型
- 拆箱：将包装类型转化为基本数据类型

## 2、装箱与自动装箱的区别
 &emsp; &emsp;装箱：利用Integer的构造方法Integer（int value），即Integer c=new Integer(1);<br>
 &emsp; &emsp;自动装箱：或者叫隐式装箱，直接给Integer赋值，即Integer d=1,在编译的时候，会调用Integer.valueOf（）方法完成装箱。<br>
 &emsp; &emsp;相比而言，自动装箱可能比装箱具有更高的效率，体现在自动装箱的缓存上，下面从几道题目来讲自动装箱的缓存。


## 3、new Integer(123) 与 Integer.valueOf(123) 的区别在于:
 &emsp; &emsp;new Integer(123): 每次都会新建一个对象<br>
 &emsp; &emsp;Integer.valueOf(123):   会使用缓存池中的对象，多次调用会取得同一个对象的引用<br>
 &emsp; &emsp;Integer 缓存池的大小默认为 -128~127

# 二、实现思路

以Interger类为例，下面看一段代码：

```
public class Main {
    public static void main(String[] args) {  
        Integer i = 10;
        int n = i;
    }
}
```



 &emsp; &emsp;装箱过程是通过调用包装器的valueOf方法实现的，而拆箱过程是通过调用包装器的 xxxValue方法实现的。（xxx代表对应的基本数据类型）


# 三、相关面试题
以下代码的输出结果为？

```
public class TestBox2 {
    public static void main(String[] args) {
        Integer a = 100;
        Integer b = 100;
        Integer c = 200;
        Integer d = 200;
        System.out.println(a == b);
        System.out.println(c == d);
    }
}

//运行结果： TRUE
//          False
```



 &emsp; &emsp;看源码可知：在通过valueOf方法创建Integer对象的时候，如果数值在[-128,127]之间，便返回指向IntegerCache.cache中已经存在的对象的引用；否则创建一个新的Integer对象。<br>

 &emsp; &emsp;其他数据类型的valueOf方法各有不同，可以去看源码，或者参考下面文章

参考：https://www.cnblogs.com/dolphin0520/p/3780005.html

