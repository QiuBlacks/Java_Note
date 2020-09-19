# ==concurrenthashmap==

# 一、基本介绍
## 1、为什么要用ConcurrentHashMap
随着并发量的增加，HashMap并没有使用同步，在多线程情况下使用HashMap的时候就会出现并发问题，不安全。而HashTable虽然是安全的，但是使用的是synchronized 锁整表操作，当多线程并发的情况下，都要竞争同一把锁，导致效率极其低下，这样在性能上将会产生很大的影响。而在JDK1.5后为了改进Hashtable的痛点，ConcurrentHashMap应运而生。

ConcurrentHashMap只是**针对put方法进行了加锁**，而对于get方法并没有采用加锁的操作，因为具体的值，在Segment的HashEntry里面的Node等**变量是volatile**的，基于happens-before（先行发生）原则，对数据的写先行发生于对数据的读，所以再读取的时候获取到的必然是最新的结果。

因为对数组的操作，在主内存和工作内存中，load和use、assgin和store是必然连在一起的，一旦使用（use）发生，那load必先行发生于use之前，use前必然从主内存中加载最新的值到工作内存的变量副本里。而一旦赋值（assgin），必然先行发生于store将值传递给主内存，在write到主内存中去。==所以get方式无需加锁也能获取到最新的结果。==


## 2、concurrenthashmap1.7和1.8
## 1） JDK1.7：
- 使用分段锁机制实现；即Segment 通过继承ReentrantLock来进行加锁
- 两个静态内部类 HashEntry链表数组 和 Segment数组；
- 查询遍历链表效率太慢；

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200906164739680.png" alt="image-20200906164739680" style="zoom:67%;" />


## 2）JDK1.8
- 使用数组+链表+红黑树数据结构，加锁则采用CAS和synchronized实现
- 将 1.7 中存放数据的 HashEntry 改为 Node，但作用都是相同的

  

---




# ==concurrenthashmap1.7==

# 一、基本介绍
1、在JDK1.5~1.7版本，Java使用了分段锁机制实现ConcurrentHashMap

2、ConcurrentHashMap在对象中保存了一个Segment数组，即将整个Hash表划分为多个分段；而==每个Segment元素，即每个分段则类似于一个Hashtable==；这样，在执行put操作时首先根据hash算法定位到元素属于哪个Segment，然后对该Segment加锁即可。因此，ConcurrentHashMap在多线程并发编程中可以实现多线程put操作

3、Segment数组中每个Segment里包含一个**HashEntry数组**，一个HashEntry数组中的**每个hashEntry对象是一个链表的头结点**，每个链表结构中包含的元素才是Map集合中的key-value键值对 。

4、concurrencyLevel: 并行级别、并发数、Segment 数，怎么翻译不重要，理解它。默认是 16，也就是说 ConcurrentHashMap 有 16 个 Segments，所以理论上，这个时候，最多可以同时支持 16 个线程并发写，只要它们的操作分别分布在不同的 Segment 上。这个值可以在初始化的时候设置为其他值，但是一旦初始化以后，它是不可以扩容的。

==注意==：Segment内部是由**数组+链表**组成的。



# 二、数据结构
## 1、结构介绍

==数组+链表==	

concurrenthashmap包含两个静态内部类 **HashEntry** 和 **Segment**；

HashEntry用来封装映射表的键值对；Segment用来充当锁的角色，每个 Segment对象守护整个散列映射表的若干个桶。每个桶是由若干个 HashEntry 对象链接起来的链表。一个 ConcurrentHashMap 实例中包含由若干个 Segment 对象组成的数组。

简单理解就是，ConcurrentHashMap 是一个 Segment 数组，==Segment 通过继承ReentrantLock来进行加锁==，所以每次需要加锁的操作锁住的是一个segment，这样只要保证每个Segment是线程安全的，也就实现了全局的线程安全。

## 2、图解
<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200906164609220.png" alt="image-20200906164609220" style="zoom:67%;" />



# 三、实现原理

## 1、初始化
### 1）参数介绍
- **initialCapacity**: 初始容量，这个值指的是整个 ConcurrentHashMap 的初始容量，实际操作的时候需要平均分给每个 Segment。
- **loadFactor**: 负载因子，之前我们说了，Segment 数组不可以扩容，所以这个负载因子是给每个 Segment 内部使用的。

### 2）源码分析
```java
public ConcurrentHashMap(int initialCapacity, float loadFactor, int concurrencyLevel) {
    if (!(loadFactor > 0) || initialCapacity < 0 || concurrencyLevel <= 0)
        throw new IllegalArgumentException();
    if (concurrencyLevel > MAX_SEGMENTS)
        concurrencyLevel = MAX_SEGMENTS;
    // Find power-of-two sizes best matching arguments
    int sshift = 0;
    int ssize = 1;    

    // 计算并行级别 ssize，因为要保持并行级别是 2 的 n 次方
    while (ssize < concurrencyLevel) {
        ++sshift;
        ssize <<= 1;
    }   
     // 我们这里先不要那么烧脑，用默认值，concurrencyLevel 为 16，sshift 为 4
    // 那么计算出 segmentShift 为 28，segmentMask 为 15，后面会用到这两个值
    this.segmentShift = 32 - sshift;
    this.segmentMask = ssize - 1;

    if (initialCapacity > MAXIMUM_CAPACITY)
    initialCapacity = MAXIMUM_CAPACITY;

    // initialCapacity 是设置整个 map 初始的大小，
    // 这里根据 initialCapacity 计算 Segment 数组中每个位置可以分到的大小
    // 如 initialCapacity 为 64，那么每个 Segment 或称之为"槽"可以分到 4 个
    int c = initialCapacity / ssize;
    if (c * ssize < initialCapacity)
        ++c;
    // 默认 MIN_SEGMENT_TABLE_CAPACITY 是 2，这个值也是有讲究的，因为这样的话，对于具体的槽上，
    // 插入一个元素不至于扩容，插入第二个的时候才会扩容
    int cap = MIN_SEGMENT_TABLE_CAPACITY; 
    while (cap < c)
        cap <<= 1;
        
     // 创建 Segment 数组，
    // 并创建数组的第一个元素 segment[0]
    Segment<K,V> s0 =
        new Segment<K,V>(loadFactor, (int)(cap * loadFactor),
                         (HashEntry<K,V>[])new HashEntry[cap]);
    Segment<K,V>[] ss = (Segment<K,V>[])new Segment[ssize];
    // 往数组写入 segment[0]
    UNSAFE.putOrderedObject(ss, SBASE, s0); // ordered write of segments[0]
    this.segments = ss;
}   
```
初始化完成，我们得到了一个 Segment 数组。<br> 
我们就当是用 new ConcurrentHashMap() 无参构造函数进行初始化的，那么初始化完成后: <br>

- Segment 数组长度为 16，不可以扩容
- Segment[i] 的默认大小为 2，负载因子是 0.75，得出初始阈值为 1.5，也就是以后插入第一个元素不会触发扩容，插入第二个会进行第一次扩容 
- 这里初始化了 segment[0]，其他位置还是 null，至于为什么要初始化 segment[0]，后面的代码会介绍 
- 当前 segmentShift 的值为 32 - 4 = 28，segmentMask 为 16 - 1 = 15，姑且把它们简单翻译为移位数和掩码，这两个值马上就会用到

## 2、扩容： rehash

注意：segment 数组不能扩容，扩容是 segment 数组某个位置内部的数组 HashEntry<K,V>[] 进行扩容，扩容后，容量为原来的 ==**2 倍**==。

### 1）源码分析
```java
// 方法参数上的 node 是这次扩容后，需要添加到新的数组中的数据。
private void rehash(HashEntry<K,V> node) {
    HashEntry<K,V>[] oldTable = table;
    int oldCapacity = oldTable.length;
    // 2 倍
    int newCapacity = oldCapacity << 1;
    threshold = (int)(newCapacity * loadFactor);
    // 创建新数组
    HashEntry<K,V>[] newTable =
        (HashEntry<K,V>[]) new HashEntry[newCapacity];
    // 新的掩码，如从 16 扩容到 32，那么 sizeMask 为 31，对应二进制 ‘000...00011111’
    int sizeMask = newCapacity - 1;

    // 遍历原数组，老套路，将原数组位置 i 处的链表拆分到 新数组位置 i 和 i+oldCap 两个位置
    for (int i = 0; i < oldCapacity ; i++) {
        // e 是链表的第一个元素
        HashEntry<K,V> e = oldTable[i];
        if (e != null) {
            HashEntry<K,V> next = e.next;
            // 计算应该放置在新数组中的位置，
            // 假设原数组长度为 16，e 在 oldTable[3] 处，那么 idx 只可能是 3 或者是 3 + 16 = 19
            int idx = e.hash & sizeMask;
            if (next == null)   // 该位置处只有一个元素，那比较好办
                newTable[idx] = e;
            else { // Reuse consecutive sequence at same slot
                // e 是链表表头
                HashEntry<K,V> lastRun = e;
                // idx 是当前链表的头结点 e 的新位置
                int lastIdx = idx;

                // 下面这个 for 循环会找到一个 lastRun 节点，这个节点之后的所有元素是将要放到一起的
                for (HashEntry<K,V> last = next;
                     last != null;
                     last = last.next) {
                    int k = last.hash & sizeMask;
                    if (k != lastIdx) {
                        lastIdx = k;
                        lastRun = last;
                    }
                }
                // 将 lastRun 及其之后的所有节点组成的这个链表放到 lastIdx 这个位置
                newTable[lastIdx] = lastRun;
                // 下面的操作是处理 lastRun 之前的节点，
                //    这些节点可能分配在另一个链表中，也可能分配到上面的那个链表中
                for (HashEntry<K,V> p = e; p != lastRun; p = p.next) {
                    V v = p.value;
                    int h = p.hash;
                    int k = h & sizeMask;
                    HashEntry<K,V> n = newTable[k];
                    newTable[k] = new HashEntry<K,V>(h, p.key, v, n);
                }
            }
        }
    }
    // 将新来的 node 放到新数组中刚刚的 两个链表之一 的 头部
    int nodeIndex = node.hash & sizeMask; // add the new node
    node.setNext(newTable[nodeIndex]);
    newTable[nodeIndex] = node;
    table = newTable;
}
```



## 3、put 过程分析

## 1）过程分析
- 根据 key 计算出对应的 hash 值
- 根据 hash 值找到对应的Segment 对象：
- 在这个 Segment 中执行具体的 put 操作：
  - 将当前 Segment 加锁，通过 key 的 hashcode 定位到 HashEntry
  - 遍历该 HashEntry， 如果不为空则判断传入的 key 和当前遍历的 key 是否相等，
    相等则覆盖旧的 value。
  - 为空则需要新建一个 HashEntry 并加入到 Segment 中， 同时会先判断是否需
    要扩容。
  -  最后会解除在 1 中所获取当前 Segment 的锁  

## 2）源码分析
```java
public V put(K key, V value) {
    Segment<K,V> s;
    if (value == null)
        throw new NullPointerException();
    // 1. 计算 key 的 hash 值
    int hash = hash(key);
    // 2. 根据 hash 值找到 Segment 数组中的位置 j
    //    hash 是 32 位，无符号右移 segmentShift(28) 位，剩下高 4 位，
    //    然后和 segmentMask(15) 做一次与操作，也就是说 j 是 hash 值的高 4 位，也就是槽的数组下标
    int j = (hash >>> segmentShift) & segmentMask;
    // 刚刚说了，初始化的时候初始化了 segment[0]，但是其他位置还是 null，
    // ensureSegment(j) 对 segment[j] 进行初始化
    if ((s = (Segment<K,V>)UNSAFE.getObject          // nonvolatile; recheck
         (segments, (j << SSHIFT) + SBASE)) == null) //  in ensureSegment
        s = ensureSegment(j);
    // 3. 插入新值到 槽 s 中
    return s.put(key, hash, value, false);
}
```
## 4、get

- 计算 hash 值，找到 segment 数组中的具体位置，或我们前面用的“槽”
- 槽中也是一个数组，根据 hash 找到数组中具体的位置
- 到这里是链表了，顺着链表进行查找即可

```java
public V get(Object key) {
    Segment<K,V> s; // manually integrate access methods to reduce overhead
    HashEntry<K,V>[] tab;
    // 1. hash 值
    int h = hash(key);
    long u = (((h >>> segmentShift) & segmentMask) << SSHIFT) + SBASE;
    // 2. 根据 hash 找到对应的 segment
    if ((s = (Segment<K,V>)UNSAFE.getObjectVolatile(segments, u)) != null &&
        (tab = s.table) != null) {
        // 3. 找到segment 内部数组相应位置的链表，遍历
        for (HashEntry<K,V> e = (HashEntry<K,V>) UNSAFE.getObjectVolatile
                 (tab, ((long)(((tab.length - 1) & h)) << TSHIFT) + TBASE);
             e != null; e = e.next) {
            K k;
            if ((k = e.key) == key || (e.hash == h && key.equals(k)))
                return e.value;
        }
    }
    return null;
}
```






# ==ConcurrentHashMap JDK1.8==
在JDK1.7之前，ConcurrentHashMap是通过分段锁机制来实现的，所以其最大并发度受Segment的个数限制。

因此，在JDK1.8中，ConcurrentHashMap的实现原理摒弃了这种设计，而是选择了与HashMap类似的**数组+链表+红黑树**的方式实现，而加锁则采用**CAS和synchronized**实现

将 1.7 中存放数据的 HashEntry 改为 Node

Segment数组仍然保留

# 一、数据结构
![image-20200627112826118](https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200627112826118.png)





# 二、底层实现

## 1、初始化
```java
// 这构造函数里，什么都不干
public ConcurrentHashMap() {
}
public ConcurrentHashMap(int initialCapacity) {
    if (initialCapacity < 0)
        throw new IllegalArgumentException();
    int cap = ((initialCapacity >= (MAXIMUM_CAPACITY >>> 1)) ?
               MAXIMUM_CAPACITY :
               tableSizeFor(initialCapacity + (initialCapacity >>> 1) + 1));
    this.sizeCtl = cap;
}
```
这个初始化方法有点意思，通过提供初始容量，计算了 sizeCtl，sizeCtl = 【 (1.5 * initialCapacity + 1)，然后向上取最近的 2 的 n 次方】。如 initialCapacity 为 10，那么得到 sizeCtl 为 16，如果 initialCapacity 为 11，得到 sizeCtl 为 32。

## 2、  put 过程分析
### 1）put流程
- 根据 key 计算出 hashcode 。检查key/value是否为null，如果为null，则抛异常
- 判断是否需要进行初始化，如果没有，则调用initTable()进行初始化
- 根据key的hash值计算出其应该在table中储存的位置i，f 即为当前 key 定位出的 Node（即table[i]），根据f的不同有如下三种情况：
  -  如果**table[i]==null**(即该位置的节点为空，没有发生碰撞)，则利用**CAS操作**直接存储在该位置，如果CAS操作成功则退出死循环（失败则自旋保证成功）
  - 如果table[i]!=null(即该位置已经有其它节点，发生碰撞)，碰撞处理也有两种情况
    - 如果当前位置table[i]的 **hashcode == MOVED == -1,**即正在扩容，帮助其扩容
    - 说明table[i]的节点的hash值不等于MOVED，**synchronized锁**住**头结点table[i]**，进行插入操作 (分为**链表写入和红黑树写入**）   
- 如果table[i]的节点是链表节点，则检查table的第i个位置的链表的元素个数是否大于了8，**大于8就需要转化为红黑树**，如果需要则调用treeifyBin函数进行转化。
- 插入成功后，如果key已经存在，返回oldValue；key开始不存在，返回null。

###  2）代码实现
```java
public V put(K key, V value) {
    return putVal(key, value, false);
}
final V putVal(K key, V value, boolean onlyIfAbsent) {
    if (key == null || value == null) throw new NullPointerException();
    // 得到 hash 值
    int hash = spread(key.hashCode());
    // 用于记录相应链表的长度
    int binCount = 0;
    for (Node<K,V>[] tab = table;;) {
        Node<K,V> f; int n, i, fh;
        // 如果数组"空"，进行数组初始化
        if (tab == null || (n = tab.length) == 0)
            // 初始化数组，后面会详细介绍
            tab = initTable();

        // 找该 hash 值对应的数组下标，得到第一个节点 f
        else if ((f = tabAt(tab, i = (n - 1) & hash)) == null) {
            // 如果数组该位置为空，
            // 用一次 CAS 操作将这个新值放入其中即可，这个 put 操作差不多就结束了，可以拉到最后面了
            // 如果 CAS 失败，那就是有并发操作，进到下一个循环就好了
            if (casTabAt(tab, i, null,
                         new Node<K,V>(hash, key, value, null)))
                break;                   // no lock when adding to empty bin
        }
        // hash 居然可以等于 MOVED，这个需要到后面才能看明白，不过从名字上也能猜到，肯定是因为在扩容
        else if ((fh = f.hash) == MOVED)
            // 帮助数据迁移，这个等到看完数据迁移部分的介绍后，再理解这个就很简单了
            tab = helpTransfer(tab, f);

        else { // 到这里就是说，f 是该位置的头结点，而且不为空

            V oldVal = null;
            // 获取数组该位置的头结点的监视器锁
            synchronized (f) {
                if (tabAt(tab, i) == f) {
                    if (fh >= 0) { // 头结点的 hash 值大于 0，说明是链表
                        // 用于累加，记录链表的长度
                        binCount = 1;
                        // 遍历链表
                        for (Node<K,V> e = f;; ++binCount) {
                            K ek;
                            // 如果发现了"相等"的 key，判断是否要进行值覆盖，然后也就可以 break 了
                            if (e.hash == hash &&
                                ((ek = e.key) == key ||
                                 (ek != null && key.equals(ek)))) {
                                oldVal = e.val;
                                if (!onlyIfAbsent)
                                    e.val = value;
                                break;
                            }
                            // 到了链表的最末端，将这个新值放到链表的最后面
                            Node<K,V> pred = e;
                            if ((e = e.next) == null) {
                                pred.next = new Node<K,V>(hash, key,
                                                          value, null);
                                break;
                            }
                        }
                    }
                    else if (f instanceof TreeBin) { // 红黑树
                        Node<K,V> p;
                        binCount = 2;
                        // 调用红黑树的插值方法插入新节点
                        if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key,
                                                       value)) != null) {
                            oldVal = p.val;
                            if (!onlyIfAbsent)
                                p.val = value;
                        }
                    }
                }
            }

            if (binCount != 0) {
                // 判断是否要将链表转换为红黑树，临界值和 HashMap 一样，也是 8
                if (binCount >= TREEIFY_THRESHOLD)
                    // 这个方法和 HashMap 中稍微有一点点不同，那就是它不是一定会进行红黑树转换，
                    // 如果当前数组的长度小于 64，那么会选择进行数组扩容，而不是转换为红黑树
                    //    具体源码我们就不看了，扩容部分后面说
                    treeifyBin(tab, i);
                if (oldVal != null)
                    return oldVal;
                break;
            }
        }
    }
    // 
    addCount(1L, binCount);
    return null;
}
```

## 3、get
### 1）流程分析
- 计算 hash 值 根据 hash 值找到数组对应位置: (n - 1) & h 根据该位置处结点性质进行相应查找
  - 如果该位置为 null，那么直接返回 null 就可以了
  - 如果该位置处的节点刚好就是我们需要的，返回该节点的值即可
- 如果该位置节点的 hash 值小于 0，说明正在扩容；
- 或者是**红黑树**，就按照树的方式获取值  
- 后面我们再介绍 find 方法 如果以上 3 条都不满足，那就是**链表**，进行遍历比对即可

## 4、扩容：tryPresize
这个方法要完完全全看懂还需要看之后的 transfer 方法，读者应该提前知道这点。

扩容后数组容量为原来的 2 倍。
### 1）
核心在于 sizeCtl 值的操作，首先将其设置为一个负数，然后执行 transfer(tab, null)，再下一个循环将 sizeCtl 加 1，并执行 transfer(tab, nt)，之后可能是继续 sizeCtl 加 1，并执行 transfer(tab, nt)。


## 5、数据迁移: transfer





# 三、重要知识点

## 1、与hashmap不同之一

底层结构存放的是TreeBin对象，而不是TreeNode对象；TreeBin包装的很多TreeNode节点，它代替了TreeNode的根节点，也就是说在实际的ConcurrentHashMap“数组”中，存放的是TreeBin对象，而不是TreeNode对象，这是与HashMap的区别之一。









# 参考文章

[JAVA7与JAVA8中的HASHMAP和CONCURRENTHASHMAP知识点总结](https://www.cnblogs.com/theRhyme/p/9404082.html)

[JUC集合: ConcurrentHashMap详解](https://www.pdai.tech/md/java/thread/java-thread-x-juc-collection-ConcurrentHashMap.html)

[HashMap? ConcurrentHashMap? 相信看完这篇没人能难住你](https://crossoverjie.top/2018/07/23/java-senior/ConcurrentHashMap/)

[探索 ConcurrentHashMap 高并发性的实现机制](https://www.ibm.com/developerworks/cn/java/java-lo-concurrenthashmap/index.html)

[一文彻底搞懂ConcurrentHashMap原理](https://www.itqiankun.com/article/concurrenthashmap-principle#%E6%80%BB%E7%BB%93)