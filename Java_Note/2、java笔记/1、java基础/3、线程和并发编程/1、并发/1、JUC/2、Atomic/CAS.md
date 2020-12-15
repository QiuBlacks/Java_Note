# CAS算法

 Compare And Swap

适合于并发量小的场景

JUC下的原子类好像都是基于CAS实现的

# 一、基本介绍

## 1、 基本概念

即compare and swap（比较与交换），是一种有名的无锁算法。无锁编程，即不使用锁的情况下实现多线程之间的变量同步，也就是在没有线程被阻塞的情况下实现变量的同步，所以也叫非阻塞同步（Non-blocking Synchronization）。

CAS算法涉及到三个操作数

- 需要读写的内存值地址V

- 进行比较的值 A
- 拟写入的新值 B

当且仅当 V 的值等于 A时，CAS通过原子方式用新值B来更新V的值，否则不会执行任何操作（比较和替换是一个原子操作）。一般情况下是一个自旋操作，即不断的重试。



## 2、出现问题及解决方法

#### 1 ）ABA 问题

如果一个变量V初次读取的时候是A值，并且在准备赋值的时候检查到它仍然是A值，那我们就能说明它的值没有被其他线程修改过了吗？很明显是不能的，因为在**这段时间它的值可能被改为其他值，然后又改回A**，那CAS操作就会误认为它从来没有被修改过。这个问题被称为CAS操作的 "ABA"问题

JDK 1.5 以后的 **AtomicStampedReference** 类就提供了此种能力，其中的 compareAndSet 方法就是首先检查当前引用是否等于预期引用，并且当前标志是否等于预期标志，如果全部相等，则以原子方式将该引用和该标志的值设置为给定的更新值



#### 2）循环时间长开销大

自旋CAS（也就是不成功就一直循环执行直到成功）如果长时间不成功，会给CPU带来非常大的执行开销。 

如果JVM能支持处理器提供的pause指令那么效率会有一定的提升，pause指令有两个作用，第一它可以延迟流水线执行指令（de-pipeline）,使CPU不会消耗过多的执行资源，延迟的时间取决于具体实现的版本，在一些处理器上延迟时间是零。第二它可以避免在退出循环的时候因内存顺序冲突（memory order violation）而引起CPU流水线被清空（CPU pipeline flush），从而提高CPU的执行效率。

#### 3 ）只能保证一个共享变量的原子操作

CAS 只对单个共享变量有效，当操作涉及跨多个共享变量时 CAS 无效。

但是从 JDK 1.5开始，提供了AtomicReference类来保证引用对象之间的原子性，你可以把多个变量放在一个对象里来进行 CAS 操作.所以我们可以使用锁或者利用AtomicReference类**把多个共享变量合并成一个共享变量**来操作。





## 3、CAS与synchronized的使用情景

简单的来说CAS适用于写比较少的情况下（多读场景，冲突一般较少），synchronized适用于写比较多的情况下（多写场景，冲突一般较多）

1、对于资源竞争较少（线程冲突较轻）的情况，使用synchronized同步锁进行线程阻塞和唤醒切换以及用户态内核态间的切换操作额外浪费消耗cpu资源；而CAS基于硬件实现，不需要进入内核，不需要切换线程，操作自旋几率较少，因此可以获得更高的性能。

2、对于资源竞争严重（线程冲突严重）的情况，CAS自旋的概率会比较大，从而浪费更多的CPU资源，效率低于synchronized。

补充： Java并发编程这个领域中synchronized关键字一直都是元老级的角色，很久之前很多人都会称它为 “重量级锁” 。但是，在JavaSE 1.6之后进行了主要包括为了减少获得锁和释放锁带来的性能消耗而引入的 偏向锁 和 轻量级锁 以及其它各种优化之后变得在某些情况下并不是那么重了。synchronized的底层实现主要依靠 Lock-Free 的队列，基本思路是 ：自旋后阻塞，竞争切换后继续竞争锁，稍微牺牲了公平性，但获得了高吞吐量。在线程冲突较少的情况下，可以获得和CAS类似的性能；而线程冲突严重的情况下，性能远高于CAS。



# 二、底层实现

以 AtomicInteger 原子整型类为例，一起来分析下 CAS 底层实现机制。



1、查看AtomicInteger 的 compareAndSet（）方法

```
// 额外提供的compareAndSet方法
public final boolean compareAndSet(int expect, int update) {
        return unsafe.compareAndSwapInt(this, valueOffset, expect, update);
}
```

我们看到了 AtomicInteger 内部方法都是基于 Unsafe 类实现的，**Unsafe 类是个跟底层硬件CPU指令通讯的复制工具类**。

参数介绍：

（1）this，Unsafe 对象本身，需要通过这个类来获取 value 的内存偏移地址。

（2）valueOffset，value 变量的内存偏移地址。

（3）expect，期望更新的值。

（4）update，要更新的最新值。

如果原子变量中的 value 值等于 expect，则使用 update 值更新该值并返回 true，否则返回 false。

再看如何获得 valueOffset的：

```
// Unsafe实例
private static final Unsafe unsafe = Unsafe.getUnsafe();
private static final long valueOffset;

static {
        try {
              // 获得value在AtomicInteger中的偏移量
                valueOffset = unsafe.objectFieldOffset
                        (AtomicInteger.class.getDeclaredField("value"));
        } catch (Exception ex) { throw new Error(ex); }
}
// 实际变量的值
private volatile int value;
```





2、其调用了unsafe的compareAndSwapInt，它是unsafe的一个native方法

```
public final native boolean compareAndSwapInt(Object var1, long var2, int var4, int var5);
```

去查看JVM的源码你可以发现，最后是调用了汇编指令 cmpxchg，这个指令在CPU级完成CAS操作的，但在多处理器情况下必须使用lock指令加锁来完成。



## 总结

任何技术都要找到适合的场景，都不是万能的，CAS 机制也一样，也有副作用。

**问题1：**

作为乐观锁的一种实现，当多线程竞争资源激烈的情况下，而且锁定的资源处理耗时，那么其他线程就要考虑自旋的次数限制，避免过度的消耗 CPU。

另外，可以考虑上文示例代码中提到的 LongAdder 来解决，LongAdder 以空间换时间的方式，来解决 CAS 大量失败后长时间占用 CPU 资源，加大了系统性能开销的问题。

**问题2：**

A-->B--->A 问题，假设有一个变量 A ，修改为B，然后又修改为了 A，实际已经修改过了，但 CAS 可能无法感知，造成了不合理的值修改操作。

整数类型还好，如果是对象引用类型，包含了多个变量，那怎么办？加个版本号或时间戳呗，没问题！

JDK 中 java.util.concurrent.atomic 并发包下，提供了 AtomicStampedReference，通过为引用建立个 Stamp 类似版本号的方式，确保 CAS 操作的正确性。

希望此文大家收藏消化，CAS 在JDK并发包底层实现中是个非常重要的算法。











https://zhuanlan.zhihu.com/p/94762520