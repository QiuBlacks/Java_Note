# 一、==和equal

### 1、作用

### 1.1   ==  

它的作用是判断两个对象的地址是不是相等

- 对于基本类型，== 判断两个值是否相等，基本类型没有 equals() 方法
- 对于引用类型，== 判断两个变量（内存地址是否相等）是否引用同一个对象，而 equals() 判断引用的对象是否等价



### 1.2  equals()

Object.equlas()

```
public boolean equals(Object obj) {  
    return (this == obj);  
}  
```

它的作用也是**判断两个对象是否相等**。但它一般有两种使用情况

- 情况 1：类没有覆盖 equals() 方法。
  - 则通过 equals() 比较该类的两个对象时，等价于通过“==”比较这两个对象。
- 情况 2：类覆盖了 equals() 方法。
  - 一般，我们都覆盖 equals() 方法来比较两个对象的内容是否相等；若它们的内容相等，则返回 true (即，认为这两个对象相等)。



### 注意：

- String 中的 equals 方法是被重写过的，因为 **object 的 equals 方法是比较的对象的内存地址，而 String 的 equals 方法比较的是对象的值。**
- 当创建 String 类型的对象时，虚拟机会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 String 对象。
- equals：任何类型都可以调用，但是int、double这些基本类型不能使用

String.equals()

```java
public boolean equals(Object anObject) {
    	//对象直接判断
        if (this == anObject) {
            return true;
        }
    	//如果是字符串，比较他们的值value是一个char数组
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```





# 二、hashCode 与 equals 

hashCode() 的作用是**获取哈希码**，也称为散列码；它实际上是返回一个 int 整数。

这个哈希码的作用是确定该对象在哈希表中的索引位置。hashCode() 定义在 JDK 的 Object.java 中，这就意味着 Java 中的任何类都包含有 hashCode() 函数。



Java对于eqauls方法和hashCode方法是这样规定的：   

- 如果两个对象相同，那么它们的hashCode值一定要相同；
- 如果两个对象的hashCode相同，它们并不一定相同（这里说的对象相同指的是用eqauls方法比较）。 
   如不按要求去做了，会发现相同的对象可以出现在Set集合中，同时，增加新元素的效率会大大下降。
- **equals()相等的两个对象，hashcode()一定相等；equals()不相等的两个对象，却并不能证明他们的hashcode()不相等。**

换句话说，equals()方法不相等的两个对象，hashcode()有可能相等（我的理解是由于哈希码在生成的时候产生冲突造成的）。反过来，hashcode()不等，一定能推出equals()也不等；hashcode()相等，equals()可能相等，也可能不等。



在java的集合中，判断两个对象是否相等的规则是：

-  判断两个对象的hashCode是否相等
  -  如果不相等，认为两个对象也不相等，完毕返回
  -  如果相等，转入下一步
- 判断两个对象用equals运算是否相等
  -  如果不相等，认为两个对象也不相等
  - 如果相等，认为两个对象相等（equals()是判断两个对象是否相等的关键）





# 三、重写hashCode和equals

## 1、基础知识

从Object里面继承的equal方法是这样的：

```
public boolean equals(Object obj) {
        return (this == obj);
    }
```

### 1.1、为什么要同时重写hashcode方法？

未重写之前hashcode方法返回的是对象的32位JVM内存地址，那么当我们把这不同地址但是实际相等的两个对象放进HashMap里面的时候它们不被看成是同一个对象，占据了两个位置。这个跟我们的预期不符，所以要重写hashcode方法。

### 3、为什么要重写hashCode和equals

Java里是先通过hashcode判断哈希值是否相等，相等了再通过equals判断里面的值是否相等的。



## 2、测试

### 2.1、直接输出

由于new Person是不同的三个对象，默认的Person类的hashcode是根据对象的引用算的

```
public class Main {
    public static void main(String[] args) {
        HashSet<Person>  set = new HashSet<>();
        set.add(new Person(1,"qiu") );
        set.add(new Person(1,"qiu") );
        set.add(new Person(2,"qiu") );

        System.out.println(set.size());  //3

    }
}
```

### 2.2、重写hashCode和equals

```
import java.util.Arrays;

public class Person {
    int age ;
    String name;

    public Person(int age, String name) {
        this.age = age;
        this.name = name;
    }

    public Person(String name, String pwd, int age) {
        this.age =age;
        this.name = name;
        this.pwd = pwd;
    }

    @Override
    public int hashCode() {
        return age * name.hashCode();
    }

    @Override
    public boolean equals(Object obj) {
        Person person = (Person)obj;
        return name == person.name;
    }
}
```

此时输出为2





