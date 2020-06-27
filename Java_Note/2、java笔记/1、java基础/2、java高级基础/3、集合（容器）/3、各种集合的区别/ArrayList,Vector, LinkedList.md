[TOC]



# List

## 一、ArrayList,Vector, LinkedList的存储性能和特性

ArrayList和Vector都是使用数组方式存储数据，此数组元素数大于实际存储的数据以便增加和插入元素，它们都允许直接按序号索引元素，但是插入元素要涉及数组元素移动等内存操作，所以索引数据快而插入数据慢

==Vector由于使用了synchronized方法（线程安全），通常性能上较ArrayList差==

LinkedList使用双向链表实现存储，按序号索引数据需要进行前向或后向遍历，但是插入数据时只需要记录本项的前后项即可，所以插入速度较快。

如果查找一个指定位置的数据，vector和arraylist使用的时间是相同的，都是0(1),这个时候使用vector和arraylist都可以。

而如果移动一个指定位置的数据花费的时间为0(n-i)n为总长度，这个时候就应该考虑到使用linklist,因为它移动一个指定位置的数据所花费的时间为0(1),而查询一个指定位置的数据时花费的时间为0(i)。



## 二、Vector与ArrayList的区别

### 1)是否同步、安全

vector是线程同步的，所以它也是线程安全的，而arraylist是线程异步的，是不安全的。如果不考虑到线程的安全因素，一般用arraylist效率比较高。

### 2)扩容量及扩容时机（加载因子）

Vector 在数据满时（加载因子为 1）增长为原来的两倍（扩容增量：原容量的 2 倍），

而 ArrayList 在数据量达到容量的一半时（加载因子为0.5）增长为原容量的 (0.5 倍 + 1) 个空间。

如果在集合中使用数据量比较大的数据，用vector有一定的优势。

### 3)indexOf

Vector提供indexOf(obj, start)接口，ArrayList没有。

Vector 和 ArrayList 实现了同一接口 List, 但所有的 Vector 的方法都具有 synchronized 关键修饰。但对于复合操作，Vector 仍然需要进行同步处理。

```
if (!vector.contains(element))  
	vector.add(element);     
... }
```

虽然条件判断 if (!vector.contains(element))与方法调用 vector.add(element); 都是原子性的操作 (atomic)，但在 if 条件判断为真后，那个用来访问vector.contains 方法的锁已经释放，在即将的 vector.add 方法调用之间有间隙，在多线程环境中，完全有可能被其他线程获得 vector的 lock 并改变其状态, 此时当前线程的vector.add(element); 正在等待（只不过我们不知道而已）。只有当其他线程释放了 vector 的 lock 后，vector.add(element); 继续







## 三、Arraylist 与 LinkedList 区别?

### 1. 是否保证线程安全：

​	 ArrayList 和 LinkedList 都是不同步的，也就是不保证线程安全；

### 2. 底层数据结构

 Arraylist 底层使用的是 **Object 数组**；LinkedList 底层使用的是 **双向链表** 数据结构（JDK1.6之前为循环链表，JDK1.7取消了循环。注意双向链表和双向循环链表的区别，下面有介绍到！）

### 3、插入和删除是否受元素位置的影响：

 ① ArrayList 采用数组存储，所以插入和删除元素的时间复杂度受元素位置的影响。

 比如：执行add(E e)方法的时候， ArrayList 会默认在将指定的元素追加到此列表的末尾，这种情况时间复杂度就是O(1)。但是如果要在指定位置 i 插入和删除元素的话（add(int index, E element)）时间复杂度就为 O(n-i)。因为在进行上述操作的时候集合中第 i 和第 i 个元素之后的(n-i)个元素都要执行向后位/向前移一位的操作。 

② LinkedList 采用链表存储，所以对于add(E e)方法的插入，删除元素时间复杂度不受元素位置的影响，近似 O（1），如果是要在指定位置i插入和删除元素的话（(add(int index, E element)） 时间复杂度近似为o(n))因为需要先移动到指定位置再插入。

### 4、是否支持快速随机访问： 

LinkedList 不支持高效的随机元素访问，而 ArrayList 支持。快速随机访问就是通过元素的序号快速获取元素对象(对应于get(int index)方法)。

### 5、内存空间占用：

 ArrayList的空 间浪费主要体现在在list列表的结尾会预留一定的容量空间，

而LinkedList的空间花费则体现在它的每一个元素都需要消耗比ArrayList更多的空间（因为要存放直接后继和直接前驱以及数据）。













