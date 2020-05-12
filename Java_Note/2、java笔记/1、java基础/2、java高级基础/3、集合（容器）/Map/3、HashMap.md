# HashMap
---

# 一、基本介绍
## 1、特点
底层实现：数组+链表+红黑树（JDK1.8增加了红黑树部分）
![image](WEBRESOURCE21c9b52696311f17466570ae2700d2e3)

## 2、存储位置
&emsp;&emsp;当系统决定存储 HashMap 中的 key-value 对时，完全没有考虑 Entry 中的 value，仅仅只是根据 key 来计算并决定每个 Entry 的存储位置。这也说明了前面的结论：我们完全可以把 Map 集合中的 value 当成 key 的附属，当系统决定了 key 的存储位置之后，value 随之保存在那里即可。
## 3、主要属性参数

```
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
```
static class Node<K,V> implements Map.Entry<K,V> {
    final int hash;
    final K key;
    V value;
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
## 1. 确定哈希桶数组索引位置（解决哈希冲突的方法）
### 取余法：
#### 方法一：

```
static final int hash(Object key) {   //jdk1.8 & jdk1.7
     int h;
     // h = key.hashCode() 为第一步 取hashCode值
     // h ^ (h >>> 16)  为第二步 高位参与运算
     return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

#### 方法二：

```
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```


## 2、扩容机制
### 1）实现方法
&emsp;&emsp;新建一个新的数组代替已有的容量小的数组，默认长度是原来的两倍。
1.7中是先扩容后插入新值的，1.8中是先插值再扩容

### 2）具体实现（jdk1.7）

```
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

transfer()方法将原有Entry数组的元素拷贝到新的Entry数组里
  void transfer(Entry[] newTable) {
      Entry[] src = table;                   //src引用了旧的Entry数组
      int newCapacity = newTable.length;
      for (int j = 0; j < src.length; j++) { //遍历旧的Entry数组
          Entry<K,V> e = src[j];             //取得旧Entry数组的每个元素
          if (e != null) {
              src[j] = null;//释放旧Entry数组的对象引用（for循环后，旧的Entry数组不再引用任何对象）
             do {
                  Entry<K,V> next = e.next;
                 int i = indexFor(e.hash, newCapacity); //！！重新计算每个元素在数组中的位置
                 e.next = newTable[i]; //标记[1]
                 newTable[i] = e;      //将元素放在数组上
                e = next;             //访问下一个Entry链上的元素
             } while (e != null);
         }
     }
 }
```


## 3、线程安全
### 1）介绍
&emsp;&emsp;在多线程使用场景中，应该尽量避免使用线程不安全的HashMap，而使用线程安全的ConcurrentHashMap。


# 三、主要方法

## 1、put方法详解
### 1）图解
![image](WEBRESOURCE29d86685ad541c5aabbcc7df7b661458)

### 2）过程（jdk1.8的尾插法）
1.  判断当前桶是否为空，空的就需要初始化（resize 中会判断是否进行初始化）。
1.  根据当前 key 的 hashcode 定位到具体的桶中并判断是否为空，为空表明没有 Hash 冲突就直接在当前位置创建一个新桶即可。
1.  如果当前桶有值（ Hash 冲突），那么就要比较当前桶中的 key、key 的 hashcode 与写入的 key 是否相等，相等就赋值给 e,在第 8 步的时候会统一进行赋值及返回。
1. 如果当前桶为红黑树，那就要按照红黑树的方式写入数据。
1. 如果是个链表，就需要将当前的 key、value 封装成一个新节点写入到当前桶的后面（形成链表）。
1.  接着判断当前链表的大小是否大于预设的阈值，大于时就要转换为红黑树。
1.  如果在遍历过程中找到 key 相同时直接退出遍历。
1.  如果 e != null 就相当于存在相同的 key,那就需要将值覆盖。
1.  最后判断是否需要进行扩容。



### 3）jdk1.7和jdk1.8  put的区别是什么？
&emsp;&emsp;jdk1.7中使用的是 “头部插入法”，即当有新的元素加入到链表中的时候，是加在链表头部。然而，从jdk1.8以后，都修改了，执行“尾部插入法”，即插在链表的末尾。  <br>
&emsp;&emsp;使用头插会改变链表上的顺序，但是如果使用尾插，在扩容时会保持链表元素原本的顺序，就不会出现链表成环的问题了。 <br>
&emsp;&emsp;Java7在多线程操作HashMap时可能引起死循环，原因是扩容转移后前后链表顺序倒置，在转移过程中修改了原来链表中节点的引用关系。 <br>
&emsp;&emsp;Java8在同样的前提下并不会引起死循环，原因是扩容转移后前后链表顺序不变，保持之前节点的引用关系 <br>


## 2、get
&emsp;&emsp;跟put方法过程差不多，只是后面通过equals方法对于key判断算法相同，相同就返回value
- 首先将 key hash 之后取得所定位的桶。
- 如果桶为空则直接返回 null 。
- 否则判断桶的第一个位置(有可能是链表、红黑树)的 key 是否为查询的 key，是就直接返回 value。
- 如果第一个不匹配，则判断它的下一个是红黑树还是链表。
- 红黑树就按照树的查找方式返回值。
- 不然就按照链表的方式遍历匹配返回值。




# 四、遍历
## 1、遍历HashMap的键值对
第一步：根据entrySet()获取HashMap的“键值对”的Set集合。 <br>
第二步：通过Iterator迭代器遍历“第一步”得到的集合。 <br>

## 2 、遍历HashMap的键
第一步：根据keySet()获取HashMap的“键”的Set集合。 <br>
第二步：通过Iterator迭代器遍历“第一步”得到的集合。

## 3 、遍历HashMap的值
第一步：根据value()获取HashMap的“值”的集合。 <br>
第二步：通过Iterator迭代器遍历“第一步”得到的集合。

#### 参考：https://blog.csdn.net/caihaijiang/article/details/6280251
