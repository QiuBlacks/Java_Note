# String

# 一、基本介绍

**每当我们创建字符串常量时，JVM会首先检查字符串常量池，如果该字符串已经存在常量池中，那么就直接返回常量池中的实例引用。如果字符串不存在常量池中，就会实例化该字符串并且将其放到常量池中。由于String字符串的不可变性我们可以十分肯定常量池中一定不存在两个相同的字符串**



# 二、底层实现

1、

jdk1.8时采用char[]进行存储；jdk1.9之后采用byte[]进行存储；



如果在**拼接前后出现变量**，则连接的结果存放在堆中字符串常量池外（相当于new一个对象），原理是StringBuilder;

```java
public static void main(String[] args) {
        String s1 = "hello";
        String s2 = "world";
        String s3 = "helloworld";
        String s4 = "hello" + "world";
        String s5 = s1 + s2;
        System.out.println(s3 == s4); //true
        System.out.println(s3 == s5); //fasle
    }
```





Java中有2种创建字符串对象的方式

```java
String str1 = "abc";
String str2 = "abc";
System.out.println(str1 == str2);// true
```

**采用字面值**的方式创建一个字符串时，JVM首先会去字符串池中查找是否存在"abc"这个对象

如果不存在，则在字符串池中创建"abc"这个对象，然后将池中"abc"这个对象的地址赋给str1，这样str1会指向池中"abc"这个字符串对象

如果存在，则不创建任何对象，直接将池中"abc"这个对象的地址返回，赋给str2。因为str1、str2指向同一个字符串池中的"abc"对象，所以结果为true。

```java
String str3 = new String("abc");
String str4 = new String("abc");
System.out.println(str3 == str4);// false
```

**采用new关键字**新建一个字符串对象时，JVM首先在字符串池中查找有没有"abc"这个字符串对象，

如果没有，则首先在字符串池中创建一个"abc"字符串对象，然后再在堆中创建一个"abc"字符串对象，然后将堆中这个"abc"字符串对象的地址赋给str3

如果有，则不在池中再去创建"abc"这个对象了，直接在堆中创建一个"abc"字符串对象，然后将堆中的这个"abc"对象的地址赋给str4。这样，str4就指向了堆中创建的这个"abc"字符串对象；

因为str3和str4指向的是不同的字符串对象，结果为false。





# 三、常用方法

```
indexOf()：返回指定字符的索引。
charAt()：返回指定索引处的字符。
replace()：字符串替换。
trim()：去除字符串两端空白。
split()：分割字符串，返回一个分割后的字符串数组。
getBytes()：返回字符串的 byte 类型数组。
length()：返回字符串长度。
toLowerCase()：将字符串转成小写字母。
toUpperCase()：将字符串转成大写字符。
substring()：截取字符串。
equals()：字符串比较。
```

## 1、String.join(）

字符串拼接

```
    public static void main(String[] args) {
        List names=new ArrayList<String>();
        names.add("1");
        names.add("2");
        names.add("3");
        
        System.out.println(String.join("-", names));
    }
```

## 2、replaceAll（）、replace()

replace  (CharSequence target, CharSequence replacement)   ：不支持正则表达式



replaceAll (String regex,  String replacement) 支持正则表达式



###  String.intern()

检查字符串常量池中是否存在String并返回

这样做主要是为了避免在堆中不断地创建新的字符串对象

```java
public static void main(String[] args) {
        String s1 = new String("hello1");
        System.out.println(s1.intern() == s1);//false
        System.out.println(s1.intern() == "hello1");//true

        String s2 = "hello2";
        System.out.println(s2.intern() == s2);//true
    }
```







# 四、

## 1、string的最大限制长度：

字符串有长度限制

在编译期，要求字符串常量池中的常量不能超过65535，并且在javac执行过程中控制了最大值为65534。

在运行期，长度不能超过Int的范围，否则会抛异常。



参考：[Hollis的String文章](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650128788&idx=1&sn=b166e7bbc2792bce6e6835e8e4a6f9bf&chksm=f36bdcb5c41c55a3171d7b9cfbc637f014ece5036db378ab2cb10ebe8888c266690e2b1cb285&scene=126&sessionid=1589937532&key=ee7920faaaff2a8b93c9d05966d6c461ea5f3897f5e2475c3f56b82a1d6c9dc2c7680c3a1b93f3e01f2aabc5b34b15dfec47f48b82572f2d41a2eb1bb350ed17a124567376ebbc1dabb157f2422db18d&ascene=1&uin=MTg5MjE1NTkyOQ%3D%3D&devicetype=Windows+10+x64&version=6209007b&lang=zh_CN&exportkey=A1zsDDtl0QodfNyvpUvYJxQ%3D&pass_ticket=bvn6zszeDqk3OY9SoL0rKUnaFwIDPRfyYnVN4xYEATVEcM4WO%2B9hHkhpBpoW9Ilv)







## 2、字符型常量和字符串常量的区别?

1. 形式上: 字符常量是单引号引起的一个字符; 字符串常量是双引号引起的若干个字符
2. 含义上: 字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)
3. 占内存大小 字符常量只占 2 个字节; 字符串常量占若干个字节 (**注意： char 在 Java 中占两个字节**)