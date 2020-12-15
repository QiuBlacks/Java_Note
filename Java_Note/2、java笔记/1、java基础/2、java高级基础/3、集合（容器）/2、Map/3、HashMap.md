# HashMap

# 一、基本介绍

## 1、特点

解决冲突：==链地址法（拉链法）==

底层实现：数组+链表+红黑树（JDK1.8增加了红黑树部分）

允许空键 和 空值

![image-20200603233725215](https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200603233725215.png)



## 2、存储位置

当系统决定存储 HashMap 中的 key-value 对时，完全没有考虑 Entry 中的 value，仅仅只是根据 key 来计算并决定每个 Entry 的存储位置。这也说明了前面的结论：我们完全可以把 Map 集合中的 value 当成 key 的附属，当系统决定了 key 的存储位置之后，value 随之保存在那里即可



## 3、主要属性参数

```java
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // 默认（桶）数组长度2^4=16
static final int MAXIMUM_CAPACITY = 1 << 30; // 最大（桶）数组容量2^30
static final float DEFAULT_LOAD_FACTOR = 0.75f; // 默认负载因子
static final int TREEIFY_THRESHOLD = 8; // 链表转红黑树的阈值
static final int UNTREEIFY_THRESHOLD = 6; // 扩容时红黑树转链表的阈值
transient Node<K,V>[] table;
```





## 4、主要构造器

- HashMap()：构建一个初始容量为 16，负载因子为 0.75 的 HashMap
- HashMap(int initialCapacity)：构建一个初始容量为 initialCapacity，负载因子为 0.75 的 HashMap
- HashMap(int initialCapacity, float loadFactor)：以指定初始容量、指定的负载因子创建一个 HashMap
- public HashMap(Map<? extends K, ? extends V> m) ：包含“子Map”的构造函数



## 5、Node<K,V>[]底层实现

注意里面的四个变量：
```java
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash; //哈希值
    final K key;	//key
    V value;		//value
    Node<K,V> next;

    Node(int hash, K key, V value, Node<K,V> next) {
    }
    
    

    public final K getKey()        { return key; }
    public final V getValue()      { return value; }
    public final String toString() { return key + "=" + value; }

    public final int hashCode() {
        return Objects.hashCode(key) ^ Objects.hashCode(value);
    }

    public final V setValue(V newValue) {
        V oldValue = value;
        value = newValue;
        return oldValue;
    }

    public final boolean equals(Object o) {
        if (o == this)
            return true;
        if (o instanceof Map.Entry) {
            Map.Entry<?,?> e = (Map.Entry<?,?>)o;
            if (Objects.equals(key, e.getKey()) &&
                Objects.equals(value, e.getValue()))
                return true;
        }
        return false;
    }
}
```




# 二、底层实现
## 1 、hash():  确定哈希桶数组索引位置

让元素尽可能均匀分配在数组中相应索引处，进可能减少哈希冲突；

### 1.1   JDK7：

**通过获取key的hashCode(),然后经过4次扰动＋5次异或计算得到**

```java
static int hash(int h) {
	h ^= (h >>>20) ^ (h >>>12);
	return h ^ (h >>> 7) ^ (h >>>4);
}
```

根据h计算索引位置

```java
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```

缺陷：

```java
hash    00000000 01111010 0011 1100 0010 0000
      & 00000000 00000000 0000 0000 0000 1111
----------------------------------
        00000000 00000000 0000 0000 0000 0000
   //高位全部归零，只保留末四位 ,散列函数做的比较差
```



### 1.2   JDK8：

让哈希后的结果更均匀的分部，减少哈希碰撞，提升hashmap的运行效率

**采用一次异或，一次扰动（无符号右移一定位数）**

```java
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与异或运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

这比在**JDK 1.7**中，更为简洁，**相比在1.7中的4次位运算，5次异或运算（9次扰动），在1.8中，只进行了1次位运算和1次异或运算（2次扰动）**；





## 2、扩容机制

### 2.1   实现方法
新建一个新的数组代替已有的容量小的数组，默认长度是原来的**两倍**

1.7中是先扩容后插入新值的，1.8中是先插值再扩容

### 2.2   具体实现（jdk1.7）

```java
void resize(int newCapacity) {   //传入新的容量
      Entry[] oldTable = table;    //引用扩容前的Entry数组
      int oldCapacity = oldTable.length;         
      if (oldCapacity == MAXIMUM_CAPACITY) {  //扩容前的数组大小如果已经达到最大(2^30)了
          threshold = Integer.MAX_VALUE; //修改阈值为int的最大值(2^31-1)，这样以后就不会扩容了
          return;
      }
   
     Entry[] newTable = new Entry[newCapacity];  //初始化一个新的Entry数组
     transfer(newTable);                         //！！将数据转移到新的Entry数组里
     table = newTable;                           //HashMap的table属性引用新的Entry数组
     threshold = (int)(newCapacity * loadFactor);//修改阈值
 }
```

transfer( )  ：12-14行是形成循环链表的最大原因

```java
//transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里
  void transfer(Entry[] newTable) {
      Entry[] src = table;                   //src引用了旧的Entry数组
      int newCapacity = newTable.length;
      for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
          Entry<K,V> e = src[j];             //取得旧Entry数组的每个元素
          if (e != null) {
              src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
             do {
                 //头插法
                 Entry<K,V> next = e.next;
                 int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
                 e.next = newTable[i]; //标记[1]
                 newTable[i] = e;  	    //将元素放在数组上
                 e = next;             //访问下一个Entry链上的元素
             } while (e != null);
         }
     }
 }
```



## 3、线程安全

### 3.1   介绍
在多线程使用场景中，应该尽量避免使用线程不安全的HashMap，而使用线程安全的ConcurrentHashMap

HashMap初始容量大小为16，当put存入的数据增加时就必须扩容了，源码中会调用rehash方法（即是把原表内容移入到一个新的表中），单线程下rehash就是把原来链表遍历，从新的链表头部（为什么不加到新链表最后？因为复杂度是 O（N））挨个放入，放入的过程中依次执行函数transfer()；

### 3.2  JDK1.7多线程操作导致死循环

JDK1.7 **多线程并发**下的**resize扩容**的过程中，因为使用**头插法**， 在transfer函数中会造成元素之间会形成一个**循环链表**或**数据丢失**

```java
//保留头指针的下一个节点——因为是单链表，如果要转移头指针，一定要保存下一个结点，不然转移后链表就丢了
Entry<K,V> next = e.next;
//插入到链表的头部——e 要插入到链表的头部，所以要先用 e.next 指向新的 Hash 表第一个元素（为什么不加到新链表最后？因为复杂度是 O（N））
e.next = newTable[i];
//现在新 Hash 表的头指针仍然指向 e 没转移前的第一个元素，所以需要将新 Hash 表的头指针指向 e
newTable[i] = e;
//转移 e 的下一个结点
e = next;
```

不过，jdk 1.8 使用尾插法解决了这个问题，但是还是不建议在多线程下使用 HashMap,因为多线程下使用 HashMap 还是会存在其他问题比如**数据丢失**。并发环境下推荐使用 ConcurrentHashMap 。



### 3.3 JDK1.8put过程多线程不安全

在多线程环境下，会发生数据覆盖的情况。




# 三、主要方法

## 1、put方法详解
### 1.1  图解
![image-20200528204817290](https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200528204817290.png)

### 1.2  过程（jdk1.8的尾插法）

1.  判断当前桶是否为空，空的就需要初始化（resize 中会判断是否进行初始化）
1.  根据当前 key 的 hashcode 定位到具体的桶（索引位置）中并判断是否为空，为空表明没有 Hash 冲突就直接在当前位置创建一个新桶即可
1.  如果当前桶有值（ Hash 冲突），那么就要比较当前桶中的 key、key 的 hashcode 与写入的 key 是否相等，相等就赋值给 e，在第 8 步的时候会统一进行赋值及返回
1.  如果当前桶为红黑树，那就要按照红黑树的方式写入数据
1.  如果是个链表，就需要将当前的 key、value 封装成一个新节点写入到当前桶的后面（形成链表）
1.  接着判断当前链表的大小是否大于预设的阈值，大于时就要转换为红黑树
1.  如果在遍历过程中找到 key 相同时直接退出遍历
1.  如果 e != null 就相当于存在相同的 key，那就需要将新的值覆盖原来的
1.  最后判断是否需要进行扩容

```Java
// put方法直接调用putVal();其中onlyIfAbsent为false。表示需要改变现有的值。也就是原来节点V覆盖。
// evict 为 true 表示为插入模式，不是创建状态。
final V putVal(int hash, K key, V value, boolean onlyIfAbsent, boolean evict) {
        Node<K,V>[] tab;
    	Node<K,V> p;
    	int n, i;
        // table未初始化，或者初始化长度为0的数组
        if ((tab = table) == null || (n = tab.length) == 0)
            // 初始化table数组
            n = (tab = resize()).length;
        // 根据(n-1)&hash获得一个0~(n-1)的一个具体角标，如果数组中次角标位置为null。
        // 直接将node放入。
        if ((p = tab[i = (n - 1) & hash]) == null)
            // 创建新节点的方式也比较简单，是直接new对象而已。
            tab[i] = newNode(hash, key, value, null);
            // 如果有node，就会查看一下节点的重复情况。
        else {
            Node<K,V> e; K k;
            // 如果新插入的key和value和原来Node一样，就直接先替换为插入的新V
            if (p.hash == hash && ((k = p.key) == key || (key != null && key.equals(k))))
                e = p;
                // 如果原来的是TreeNode节点
            else if (p instanceof TreeNode)
                // 按照TreeNode节点方式插入。后文有详解
                e = ((TreeNode<K,V>)p).putTreeVal(this, tab, hash, key, value);
            else {
                // 以下就是用链表方式的Node，也就是桶中第一个node和新插入的key不同且不是红黑树的桶。
                // 依次循环桶中node
                for (int binCount = 0; ; ++binCount) {
                    // 寻找桶中第二个元素，如果为null
                    if ((e = p.next) == null) {
                        // 将新的key-value插入该节点，也就是尾部插入
                        p.next = newNode(hash, key, value, null);
                        // 如果桶中数据大于等于树化阈值8，将该桶数据进行树化处理
                        if (binCount >= TREEIFY_THRESHOLD - 1) // -1 for 1st
                            // 树化处理。注意：树化过程分两步进行。 后文有详解
                            treeifyBin(tab, hash);
                        break;
                    }
                    // 判断刚新插入的node和现在的k-v是否一样。一样就结束遍历插入， 
                    if (e.hash == hash && ((k = e.key) == key || (key != null && key.equals(k))))
                        break;
                    // 递归到下一个节点node。如果是最后一个e为null。
                    p = e;
                }
            }
            // 如果插入成功
            if (e != null) { // existing mapping for key
                V oldValue = e.value;
                // 判断是可变的node点，替换为新的value。
                if (!onlyIfAbsent || oldValue == null)
                    e.value = value;
                // hashMap做的空实现
                afterNodeAccess(e);
                // 返回旧值
                return oldValue;
            }
        }
        ++modCount;
        // 超过阈值进行扩容。
        if (++size > threshold)
            resize();
        afterNodeInsertion(evict);
        return null;
    }
```

这是jdk1.8中HashMap中put操作的主函数， 注意第6行代码，如果**没有hash碰撞则会直接插入元素**。

如果线程A和线程B同时进行put操作，刚好这两条不同的数据hash值一样，并且该位置数据为null，所以这线程A、B都会进入第6行代码中。

假设一种情况，线程A进入后还未进行数据插入时挂起，而线程B正常执行，从而正常插入数据，然后线程A获取CPU时间片，此时线程A不用再进行hash判断了，问题出现：线程A会把线程B插入的数据给覆盖，发生线程不安全。





### 1.3  jdk1.7和jdk1.8  put的区别是什么？
jdk1.7中使用的是 “头部插入法”，即当有新的元素加入到链表中的时候，是加在链表头部。然而，从jdk1.8以后，都修改了，执行“尾部插入法”，即插在链表的末尾。  
		使用头插会改变链表上的顺序，但是如果使用尾插，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。 
		Java7在多线程操作HashMap时可能引起死循环，原因是扩容转移后前后链表顺序倒置，在转移过程中修改了原来链表中节点的引用关系。 
		Java8在同样的前提下并不会引起死循环，原因是扩容转移后前后链表顺序不变，保持之前节点的引用关系 



## 2、get

跟put方法过程差不多，只是后面通过equals方法对于key判断算法相同，相同就返回value

- 首先将 key 使用hash( ) 之后取得所定位的桶
- 如果桶为空则直接返回 null 
- 否则判断桶的第一个位置(有可能是链表、红黑树)的 key 是否为查询的 key，是就直接返回 value
- 如果第一个不匹配，则判断它的下一个是红黑树还是链表
- 红黑树就按照树的查找方式返回值
- 不然就按照链表的方式遍历匹配返回值。

```java
public V get(Object key) {
        Node<K,V> e;
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }
    
final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
                do {
                    if (e.hash == hash &&
                        
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }    
```

注意：key是通过equals判断的



## 3、tableSizeFor（）

HashMap的容量总是2的整数幂，那我们初始化时传入一个容量参数，Hashmap的初始化容量又是多少呢？答案是离我们传入的参数最接近的2的整数幂，例如传入7，初始化容量为8。怎么实现的呢？这就是tableSizeFor的作用

```
static final int tableSizeFor(int cap) {
        int n = cap - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
}
```

将该数字的**最高非0位**后面全置为1  ！其利用了“拷贝”的方式，最后再加上1，就得到了我们想要的2的整数幂了!

参考 [HashMap中的tableSizeFor方法](https://blog.csdn.net/Z_ChenChen/article/details/82955221)




# 四、遍历
## 1、遍历HashMap的键值对
第一步：根据entrySet()获取HashMap的“键值对”的Set集合。 

第二步：通过Iterator迭代器遍历“第一步”得到的集合。 

```
Iterator<Map.Entry<Integer, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<Integer, String> entry = iterator.next();
    System.out.println(entry.getKey());
    System.out.println(entry.getValue());
}
```



## 2 、遍历HashMap的键
第一步：根据keySet()获取HashMap的“键”的Set集合。 

第二步：通过Iterator迭代器遍历“第一步”得到的集合。

```
Iterator<Integer> iterator = map.keySet().iterator();
        while (iterator.hasNext()) {
            Integer key = iterator.next();
            System.out.println(key);
            System.out.println(map.get(key));
        }
```



## 3 、遍历HashMap的值
第一步：根据value()获取HashMap的“值”的集合。 

第二步：通过Iterator迭代器遍历“第一步”得到的集合。

## 4、ForEach EntrySet

```
for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        }
```











# 五、重要知识点

## 1、链表转红黑树的阈值默认为8

在随机哈希代码下，桶中的节点频率遵循泊松分布，文中给出了桶长度k的频率表。由频率表可以看出，同一个Hash数组位置冲突达到8的概率非常非常小（一亿分之六）。所以作者应该是根据概率统计而选择了8作为阀值，由此可见，这个选择是非常严谨和科学的。



## 2、hashmap容器实现查找O(1)

根据get方法我们可以知道主要的查询时间花费在链表遍历上，所以HashMap的设计只要减少哈希冲突，使得链表长度尽可能的小，我们就可以视为O(1)

当然，这算是一种理想状态



## 3、HashMap为什么每次扩容都扩大2倍？ 

 主要是因为hashmap的key值经过hashcode()然后再经过hash函数之后，得到hash值，然后采用hash%n的形式，能够确定其在数组中的位置，但是这样计算速度比较慢，影响整体性能，于是采用(n-1)&hash的方式能够加快计算，但是其两者等价的前提为数组的长度为2的n次幂。







## 参考文章：

[java中HashMap详解](https://blog.csdn.net/caihaijiang/article/details/6280251)

[ HASHMAP为什么线程不安全](https://cloud.tencent.com/developer/article/1554536)

[浅析HashMap源码系列----put过程(JDK1.8版)](https://blog.csdn.net/qq_42742861/article/details/96576271)