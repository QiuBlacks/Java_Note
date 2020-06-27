

[TOC]



## 一、HashMap和HashTable、ConcurrentHashMap三者的区别

---


### 1、继承的父类不同
   &emsp; &emsp;Hashtable继承Dictionary类，而ConcurrentHashMap和HashMap继承自AbstractMap类 。但三者都实现了Map接口

###      2、线程安全性不同
 &emsp; &emsp;HashMap线程不安全,HashTable和ConcurrentHashMap线程安全。<br>
 &emsp; &emsp;但是:Hashtable的synchronized是针对整张Hash表的，即每次锁住整张表让线程独占，ConcurrentHashMap允许多个修改操作并发进行，其关键在于使用了锁分离技术



###  3、是否提供contains方法
 &emsp; &emsp;  HashMap把Hashtable的contains方法去掉了，改成containsValue和containsKey，因为contains方法容易让人引起误解。<br>
   &emsp; &emsp;Hashtable则保留了contains，containsValue和containsKey三个方法，其中contains和containsValue功能相同。

###  4、是否允许null值
 &emsp; &emsp;Hashmap是允许key和value为null值的，用containsValue和containsKey方法判断是否包含对应键值对；<br>
 &emsp; &emsp;HashTable键值对都不能为空，否则会报空指针异常。

### 5.计算hash值方式不同
#### 1)hash计算方式
 &emsp; &emsp;HashMap定义了hash函数重新计算hash值：先调用hashCode方法计算出来一个hash值，再将hash与右移16位后相异或，从而得到新的hash值。

```
static final int hash(Object key) {
        int h;
        return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
    }
```


Hashtable计算hash值，直接用key的hashCode()

#### 2）计算索引位置方法不同
 &emsp; &emsp;HashMap在求hash值对应的位置索引时，将哈希表的大小固定为了2的幂，因为是取模得到索引值，故这样取模时，不需要做除法，只需要做位运算。位运算比除法的效率要高很多。<br>
 &emsp; &emsp; 其中n=tab.length – 1
```
 index = (n - 1)&hash
```


 &emsp; &emsp;HashTable在求hash值位置索引时计算index的方法：

```
int index = (hash & 0x7FFFFFFF) % tab.length;
```

 &emsp; &emsp;&0x7FFFFFFF的目的是为了将负的hash值转化为正值，因为hash值有可能为负数，而&0x7FFFFFFF后，只有符号位改变，而后面的位都不变。

### 6.扩容方式和大小不同
当容量不足时要进行resize方法，而resize的两个步骤：<br>
- ①扩容；
- ②rehash:这里Has	hMap和HashTable都会会重新计算hash值而这里的计算方式就不同了；

 HashMap 哈希扩容必须要求为原容量的==2倍==，而且一定是2的幂次倍扩容结果，而且每次扩容时，原来数组中的元素依次重新计算存放位置，并重新插入；<br>
 &emsp; &emsp;而Hashtable扩容为原容量==2倍加1==；

### 7.解决hash冲突方式不同

Java8，HashMap中，当出现冲突时可以：<br>
 &emsp; &emsp;1）如果冲突数量小于8，则是以链表方式解决冲突。<br>
 &emsp; &emsp;2）而当冲突大于等于8时，就会将冲突的Entry转换为**红黑树进行存储。**<br>
 &emsp; &emsp;3）而又当数量小于6时，则又转化为链表存储。<br>

 &emsp; &emsp;而在HashTable中， 都是以**链表**方式存储。

### 8、遍历方式的内部实现上不同
   &emsp; &emsp;Hashtable、HashMap都使用了 Iterator。而由于历史原因，Hashtable还使用了Enumeration的方式 





## 二、HashMap 和 ConcurrentHashMap 的区别？

 &emsp; &emsp;ConcurrentHashMap和HashMap的实现方式不一样，虽然都是使用桶数组实现的，但是还是有区别，ConcurrentHashMap对桶数组进行了分段，而HashMap并没有。<br>

 &emsp; &emsp;ConcurrentHashMap在每一个分段上都用锁进行了保护。HashMap没有锁机制。所以，前者线程安全的，后者不是线程安全的。<br>
