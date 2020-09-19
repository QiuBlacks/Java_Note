# AbstractQueuedSynchronizer：AQS框架

# 一、基础知识

## 1、基本介绍

中文翻译：抽象的队列式的同步器

**并发编程中实现同步器的一个框架**

```java
public abstract class AbstractQueuedSynchronizer
    extends AbstractOwnableSynchronizer
    implements java.io.Serializable {
```

一个非常有用的超类，可用来定义锁以及依赖于排队阻塞线程的其他同步器；

ReentrantLock，ReentrantReadWriteLock，CountDownLatch，CyclicBarrier和Semaphore等这些类都是**基于AQS类实现**的。



## 2、资源共享方式

AQS维护了一个**volatile int state**（代表共享资源）和一个**FIFO线程等待队列CLH（**多线程争用资源被阻塞时会进入此队列）。

### 2.1 state的访问方式有三种:

- getState()
- setState()
- compareAndSetState()



### 2.2 AQS定义两种资源共享方式：

Exclusive：独占模式，表示共享状态值state每次能由一条线程持有，其他线程如果需要获取，则需要阻塞。如JUC中的ReentrantLock

Share：共享模式表示**共享状态值state**每次可以由多个线程持有，如JUC中的CountDownLatch



## 3、自定义同步器方法

不同的自定义同步器争用共享资源的方式也不同。**自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可**，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。

自定义同步器实现时主要实现以下几种方法：

- isHeldExclusively()：该线程是否正在独占资源。只有用到condition才需要去实现它。
- tryAcquire(int)：**独占方式。**尝试获取资源，成功则返回true，失败则返回false。
- tryRelease(int)：独占方式。尝试释放资源，成功则返回true，失败则返回false。
- tryAcquireShared(int)：**共享方式**。尝试获取资源。负数表示失败；0表示成功，但没有剩余可用资源；正数表示成功，且有剩余资源。
- tryReleaseShared(int)：共享方式。尝试释放资源，如果释放后允许唤醒后续等待结点返回true，否则返回false。





# 二、底层实现

## 1、节点Node状态：waitStatus

- **CANCELLED**(1)：表示当前结点已取消调度。当timeout或被中断（响应中断的情况下），会触发变更为此状态，进入该状态后的结点将不会再变化。
- **SIGNAL**(-1)：表示后继结点在等待当前结点唤醒。后继结点入队时，会将前继结点的状态更新为SIGNAL。
- **CONDITION**(-2)：表示结点等待在Condition上，当其他线程调用了Condition的signal()方法后，CONDITION状态的结点将**从等待队列转移到同步队列中**，等待获取同步锁。
- **PROPAGATE**(-3)：共享模式下，前继结点不仅会唤醒其后继结点，同时也可能会唤醒后继的后继结点。
- **0**：新结点入队时的默认状态。



## 2、FIFO队列

等待队列是CLH（Craig, Landin, and Hagersten）锁队列。

**通过节点中的“状态”字段来判断一个线程是否应该阻塞。当该节点的前一个节点释放锁的时候，该节点会被唤醒。**

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200918163702.png)



## 3、acquire（）

```
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    }
```























# 参考文章

[Java并发之AQS详解](https://www.cnblogs.com/waterystone/p/4920797.html)



