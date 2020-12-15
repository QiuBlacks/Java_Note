# ThreadLocal

# 一、基础知识

## 1、问题引出

通常情况下，我们创建的变量是可以被任何一个线程访问并修改的。如果想实现每一个线程都有自己的专属本地变量该如何解决呢？

 JDK 中提供的ThreadLocal类正是为了解决这样的问题。 ThreadLocal类主要解决的就是让每个线程绑定自己的值，可以将ThreadLocal类形象的比喻成存放数据的盒子，盒子中可以存储每个线程的私有数据。



## 2、定义

ThreadLocal，很多地方叫做线程本地变量，也有些地方叫做线程本地存储，其实意思差不多。

ThreadLocal为变量在每个线程中都创建了一个副本，那么**每个线程可以访问自己内部的副本变量**。

**空间换取时间**



## 3、实现过程

ThreadLocal在每个线程中对该变量会创建一个副本，即每个线程内部都会有一个该变量，且在线程内部任何地方都可以使用，线程之间互不影响，这样一来就不存在线程安全问题，也不会严重影响程序执行性能。

但是要注意，虽然ThreadLocal能够解决上面说的问题，但是由于在每个线程中都创建了副本，所以要考虑它对资源的消耗，比如内存的占用会比不使用ThreadLocal要大。

将类变量放到ThreadLocal类型的对象中，使变量在每个线程中都有独立拷贝，不会出现**一个线程读取变量时而被另一个线程修改的现象**。

## 3、使用场景

- 每个线程需要有自己单独的实例
- 实例需要在多个方法中共享，但不希望被多线程共享

最常见的ThreadLocal使用场景为用来解决数据库连接、Session管理等。

**注意：**ThreadLocal 并不解决线程间共享数据的问题



# 二、源码分析

## 1、get过程分析

get( )：获取与当前线程关联的ThreadLocal值

```java
public T get() {
		//取得当前线程
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null) {
            ThreadLocalMap.Entry e = map.getEntry(this);
            if (e != null) {
                @SuppressWarnings("unchecked")
                T result = (T)e.value;
                return result;
            }
        }
    //map为空，则初始化value
        return setInitialValue();
    }
```

### 1.1 get()方法主要做了以下事情

-  调用Thread.currentThread()获取当前线程对象t；
- 根据当前线程对象，调用getMap(Thread)获取线程对应的ThreadLocalMap对象：

```java
ThreadLocalMap getMap(Thread t) {
        return t.threadLocals;
    }
```

threadLocals：实际上就是一个ThreadLocalMap  ,这个类型是ThreadLocal类的一个内部类

```
ThreadLocal.ThreadLocalMap threadLocals = null;
```

- 如果获取的map不为空，则在map中以ThreadLocal的引用作为key来在map中获取对应的value e，否则转到步骤5；注意这里获取键值对传进去的是 this，而不是当前线程t。  
  - 若键值对e不为null，则返回e中存储的value值，否则转到步骤5；

- map为null，调用setInitialValue()方法，返回value。

```java
private T setInitialValue() {
        T value = initialValue();
        Thread t = Thread.currentThread();
        ThreadLocalMap map = getMap(t);
        if (map != null)
            map.set(this, value);
        else
            createMap(t, value);
        return value;
    }
```

对线程的ThreadLocalMap对象进行初始化操作，ThreadLocalMap对象的key为ThreadLocal对象，value为initialValue()方法的返回值null

如果map不为空，就设置键值对；

为空，再创建Map，看一下createMap的实现：

```
void createMap(Thread t, T firstValue) {
        t.threadLocals = new ThreadLocalMap(this, firstValue);
    }
```



### 1.2 总结

首先，在每个线程Thread内部有一个ThreadLocal.**ThreadLocalMap类型**的成员变量**threadLocals**，这个threadLocals就是用来存储实际的变量副本的，**键值key为当前线程的ThreadLocal变量，value为拷贝的变量副本**（即T类型的变量）。

初始时，在Thread里面，threadLocals为空，当通过ThreadLocal变量调用get()方法或者set()方法，就会对Thread类中的threadLocals进行初始化，并且以当前ThreadLocal变量为键值，以ThreadLocal要保存的副本变量为value，存到threadLocals。

然后在当前线程里面，如果要使用副本变量，就可以通过get方法在threadLocals里面查找。	



## 2、ThreadLocalMap源码分析及内存泄漏

**内存泄漏：**内存泄露为程序在申请内存后，无法释放已申请的内存空间，一次内存泄露危害可以忽略，但内存泄露堆积后果很严重，无论多少内存,迟早会被占光，

ThreadLocalMap是用来存储与线程关联的value的哈希表，它具有HashMap的部分特性，比如容量、扩容阈值等，它内部通过Entry类来存储key和value，Entry类的定义为：

```java
static class ThreadLocalMap {

        static class Entry extends WeakReference<ThreadLocal<?>> {
            /** The value associated with this ThreadLocal. */
            Object value;

            Entry(ThreadLocal<?> k, Object v) {
                super(k);
                value = v;
            }
        }
```

Entry继承自WeakReference，通过上述源码super(k)；可以知道，ThreadLocalMap是使用ThreadLocal的**弱引用**作为Key的。

分析到这里，我们可以得到下面这个对象之间的引用结构图（其中，实线为强引用，虚线为弱引用）：

**ThreadLocal内存模型：**

Threadlocal初始化的对象是存储在堆中的，而指向这个对象实例的引用则存在栈中

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200928160635.png)



**key弱引用：**ThreadLocalMap**使用ThreadLocal的弱引用作为key**，如果一个ThreadLocal没有外部关联的强引用，那么在虚拟机进行**垃圾回收GC**时，即便不手动删除，ThreadLocal仍会被回收，ThreadLocalMap在之后调用set()、getEntry()和remove()函数时会清除所有key为null的Entry。

**value强引用：**value却存在一条从Current Thread过来的强引用链。因此只有当Current Thread销毁时，value才能得到释放。

**内存泄漏原因：**ThreadLocalMap 维护 ThreadLocal 变量与具体实例的映射，当 ThreadLocal 变量被回收后（弱引用被GC），该映射的键key变为 null，该 Entry的value就会一直存在一条强引用链，无法被回收，从而造成内存泄漏。





### 2.1 ThreadLocalMap中是如何解决内存泄漏？

总结：调用ThreadLocalMap的getEntry中的getEntryAfterMiss查找key为null的位置，再通过expungeStaleEntry将key对应的value以及Entry设为null从而使得该 Entry 可被回收



在获取key对应的value时，会调用ThreadLocalMap的getEntry(ThreadLocal<?> key)方法，该方法源码如下：

```
private Entry getEntry(ThreadLocal<?> key) {
    int i = key.threadLocalHashCode & (table.length - 1);
    Entry e = table[i];
    if (e != null && e.get() == key)
        return e;
    else
        return getEntryAfterMiss(key, i, e);
}
```

通过key.threadLocalHashCode & (table.length - 1)来计算存储key的Entry的索引位置，然后判断对应的key是否存在，若存在，则返回其对应的value，否则，调用getEntryAfterMiss(ThreadLocal<?>, int, Entry)方法，源码如下：

```java
private Entry getEntryAfterMiss(ThreadLocal<?> key, int i, Entry e) {
    Entry[] tab = table;
    int len = tab.length;
 
    while (e != null) {
        ThreadLocal<?> k = e.get();
        if (k == key)
            return e;
        if (k == null)
            expungeStaleEntry(i);
        else
            i = nextIndex(i, len);
        e = tab[i];
    }
    return null;
}
```

ThreadLocalMap采用线性探查的方式来处理哈希冲突，所以会有一个while循环去查找对应的key，在查找过程中，若发现key为null，即通过弱引用的key被回收了，会调用expungeStaleEntry(int)方法，将key对应的value以及Entry设为null从而使得该 Entry 可被回收

```java
private int expungeStaleEntry(int staleSlot) {
    Entry[] tab = table;
    int len = tab.length;
 
    // 将key为null的键值对value设置为null，然后被回收
    tab[staleSlot].value = null;
    tab[staleSlot] = null;
    size--;
 
    // Rehash until we encounter null
    Entry e;
    int i;
    for (i = nextIndex(staleSlot, len);
            (e = tab[i]) != null;
            i = nextIndex(i, len)) {
        ThreadLocal<?> k = e.get();
        if (k == null) {
            e.value = null;
            tab[i] = null;
            size--;
        } else {
            int h = k.threadLocalHashCode & (len - 1);
            if (h != i) {
                tab[i] = null;
 
                // Unlike Knuth 6.4 Algorithm R, we must scan until
                // null because multiple entries could have been stale.
                while (tab[h] != null)
                    h = nextIndex(h, len);
                tab[h] = e;
            }
        }
    }
    return i;
}
```

通过上述代码可以发现，若key为null，则该方法通过下述代码来清理与key对应的value以及Entry：

```
// expunge entry at staleSlot
tab[staleSlot].value = null;
tab[staleSlot] = null;
```

此时，CurrentThread Ref不存在一条到Entry对象的强引用链，Entry到value对象也不存在强引用，那在程序运行期间，它们自然也就会被回收。



### 2.2  ThreadLocal正确的使用方法

1）每次使用完ThreadLocal都调用它的remove()方法清除数据。

在使用线程池的情况下，如果不及时进行清理，内存泄漏问题事小，甚至还会产生程序逻辑上的问题。所以，为了安全地使用ThreadLocal，必须要像每次使用完锁就解锁一样，在每次使用完ThreadLocal后都要调用remove()来清理无用的Entry。

2）将ThreadLocal变量定义成private static，这样就一直存在ThreadLocal的强引用，也就能保证任何时候都能通过ThreadLocal的弱引用访问到Entry的value值，进而清除掉 。











# 参考文章

[Java并发编程之ThreadLocal详解](https://blog.csdn.net/qq_38293564/article/details/80459827)

[Java并发编程：深入剖析ThreadLocal](https://www.cnblogs.com/dolphin0520/p/3920407.html)