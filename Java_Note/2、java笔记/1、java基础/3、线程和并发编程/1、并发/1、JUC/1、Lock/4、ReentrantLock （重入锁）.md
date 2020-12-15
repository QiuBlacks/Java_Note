# 	ReentrantLock（可重入锁） 详解
# 一、基本介绍

## 1、基本概念

### 1.1  可重入锁

自己可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。同一个线程每次获取锁，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

### 1.2   实现接口

```
public class ReentrantLock implements Lock, java.io.Serializable
```



### 1.3、内部类

![image](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200918150309.png)

ReentrantLock类内部总共存在Sync、NonfairSync、FairSync三个类; NonfairSync与FairSync类继承自Sync类,正好对应了ReentrantLock的非公平锁、公平锁两大类型。 



## 2、主要方法

```java
//查询当前线程保持此锁的次数。
int getHoldCount() 

//返回目前拥有此锁的线程，如果此锁不被任何线程拥有，则返回 null。      
protected  Thread   getOwner(); 

//返回一个 collection，它包含可能正等待获取此锁的线程，其内部维持一个队列，这点稍后会分析。      
protected  Collection<Thread>   getQueuedThreads(); 

//返回正等待获取此锁的线程估计数。   
int getQueueLength();

// 返回一个 collection，它包含可能正在等待与此锁相关给定条件的那些线程。
protected  Collection<Thread>   getWaitingThreads(Condition condition); 

//返回等待与此锁相关的给定条件的线程估计数。       
int getWaitQueueLength(Condition condition);

// 查询给定线程是否正在等待获取此锁。     
boolean hasQueuedThread(Thread thread); 

//查询是否有些线程正在等待获取此锁。     
boolean hasQueuedThreads();

//查询是否有些线程正在等待与此锁有关的给定条件。     
boolean hasWaiters(Condition condition); 

//如果此锁的公平设置为 true，则返回 true。     
boolean isFair() 

//查询当前线程是否保持此锁。      
boolean isHeldByCurrentThread() 

//查询此锁是否由任意线程保持。        
boolean isLocked()       
```





## 3、使用方法

ReentrantLock是Java并发包中互斥锁，它有公平锁和非公平锁两种实现方式，以lock()为例，其使用方式为：

```java
ReentrantLock lock = new ReentrantLock();

    // 获取锁
    lock.lock();
	//支持重入锁
    lock.lock();
    try {

        // 业务逻辑

    } finally {
    	//执行两次释放锁
        lock.unlock();
        lock.unlock();
	}
```

## 4、实现原理
 里面的很多操作都是**通过CAS抢占锁**

重入锁ReentrantLock，是一个基于AQS并发框架的并发控制类，其内部实现了3个类，分别是Sync、NoFairSync以及FairSync类，其中Sync继承自AQS，实现了释放锁的模板方法tryRelease(int)，而NoFairSync和FairSync都继承自Sync，实现各种获取锁的方法tryAcquire(int)。ReentrantLock的所有方法实现几乎都间接调用了这3个类，因此当我们在使用ReentrantLock时，大部分使用都是在间接调用AQS同步器中的方法，



# 二、实现流程

![image](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200918150318.jpeg)

 首先，ReentrantLock的lock()方法会调用其内部成员变量sync的lock()方法；

 其次，sync的非公平锁NonfairSync或公平锁FairSync实现了父类AbstractQueuedSynchronizer的lock()方法，其会调用acquire()方法；

 然后，acquire()方法则在sync父类AbstractQueuedSynchronizer中实现，它只有一段代码：

```
public final void acquire(int arg) {
    if (!tryAcquire(arg) && acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
    	selfInterrupt();
}
```
 通过tryAcquire()方法试图获取锁，获取到直接返回结果，否则通过嵌套调用acquireQueued()、addWaiter()方法将请求获取锁的线程加入等待队列，如果成功的话，将当前请求线程阻塞



# 三、源码分析

**关于acquire的源码分析看AQS里的**



## 1、Sync类
### 1）源码分析
```java
abstract static class Sync extends AbstractQueuedSynchronizer {
    // 序列号
    private static final long serialVersionUID = -5179523762034025860L;
    
    // 获取锁
    abstract void lock();
    
    // 非公平方式获取
    final boolean nonfairTryAcquire(int acquires) {
        // 当前线程
        final Thread current = Thread.currentThread();
        // 获取状态
        int c = getState();
        if (c == 0) { // 表示没有线程正在竞争该锁
            if (compareAndSetState(0, acquires)) { // 比较并设置状态成功，状态0表示锁没有被占用
                // 设置当前线程独占
                setExclusiveOwnerThread(current); 
                return true; // 成功
            }
        }
        else if (current == getExclusiveOwnerThread()) { // 当前线程拥有该锁
            int nextc = c + acquires; // 增加重入次数
            if (nextc < 0) // overflow
                throw new Error("Maximum lock count exceeded");
            // 设置状态
            setState(nextc); 
            // 成功
            return true; 
        }
        // 失败
        return false;
    }
    
    // 试图在共享模式下获取对象状态，此方法应该查询是否允许它在共享模式下获取对象状态，如果允许，则获取它
    protected final boolean tryRelease(int releases) {
        int c = getState() - releases;
        if (Thread.currentThread() != getExclusiveOwnerThread()) // 当前线程不为独占线程
            throw new IllegalMonitorStateException(); // 抛出异常
        // 释放标识
        boolean free = false; 
        if (c == 0) {
            free = true;
            // 已经释放，清空独占
            setExclusiveOwnerThread(null); 
        }
        // 设置标识
        setState(c); 
        return free; 
    }
    
    // 判断资源是否被当前线程占有
    protected final boolean isHeldExclusively() {
        // While we must in general read state before owner,
        // we don't need to do so to check if current thread is owner
        return getExclusiveOwnerThread() == Thread.currentThread();
    }

    // 新生一个条件
    final ConditionObject newCondition() {
        return new ConditionObject();
    }

    // Methods relayed from outer class
    // 返回资源的占用线程
    final Thread getOwner() {        
        return getState() == 0 ? null : getExclusiveOwnerThread();
    }
    // 返回状态
    final int getHoldCount() {            
        return isHeldExclusively() ? getState() : 0;
    }

    // 资源是否被占用
    final boolean isLocked() {        
        return getState() != 0;
    }

    /**
        * Reconstitutes the instance from a stream (that is, deserializes it).
        */
    // 自定义反序列化逻辑
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        s.defaultReadObject();
        setState(0); // reset to unlocked state
    }
}　　
```
### 2）主要方法：
![image](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200918150327.png)



## 2、NonfairSync类

### 1）源码分析
&emsp;NonfairSync类继承了Sync类，表示采用非公平策略获取锁，其实现了Sync类中抽象的lock方法，源码如下:

```java
// 非公平锁
static final class NonfairSync extends Sync {
    // 版本号
    private static final long serialVersionUID = 7316153563782823691L;

    // 获得锁
    final void lock() {
         //执行CAS操作，获取同步状态（锁）
        //尝试把state的状态从0设置为1，如果返回true则代表获取锁成功
        if (compareAndSetState(0, 1))
            // 成功则把当前线程设置独占了锁
            setExclusiveOwnerThread(Thread.currentThread());
        else // 锁已经被占用，或者set失败
           //否则再次请求同步状态
            acquire(1); 
    }
}
```
**关于acquire的源码分析看AQS里的**



### 2）说明: 

从lock方法的源码可知，**每一次都尝试获取锁**，而并不会按照公平等待的原则进行等待，无视等待队列的存在而抢占锁；抢占不成功的话，则调用父类的acquire()方法，按照上面讲的，继而会调用tryAcquire()方法，通过进入等待队列的方式排队获取锁



## 3、FairSyn类

### 2.1 源码分析

在获取锁的时，公平锁的获取顺序是完全遵循时间上的**FIFO规则**，也就是说先请求的线程一定会先获取锁，后来的线程肯定需要排队

FairSync类也继承了Sync类，表示采用公平策略获取锁，其实现了Sync类中的抽象lock方法，源码如下:

```java
// 公平锁
static final class FairSync extends Sync {
    // 版本序列化
    private static final long serialVersionUID = -3000897897090466540L;

    final void lock() {
        // 以独占模式获取对象，忽略中断
        acquire(1);
    }

    /**
        * Fair version of tryAcquire.  Don't grant access unless
        * recursive call or no waiters or is first.
        */
    // 尝试公平获取锁
    protected final boolean tryAcquire(int acquires) {
        // 获取当前线程
        final Thread current = Thread.currentThread();
        // 获取状态
        int c = getState();
        if (c == 0) { // 状态为0
            if (!hasQueuedPredecessors() &&
                compareAndSetState(0, acquires)) { // 不存在已经等待更久的线程并且比较并且设置状态成功
                // 设置当前线程独占
                setExclusiveOwnerThread(current);
                return true;
            }
        }
        else if (current == getExclusiveOwnerThread()) { // 状态不为0，即资源已经被线程占据
            // 下一个状态
            int nextc = c + acquires;
            if (nextc < 0) // 超过了int的表示范围
                throw new Error("Maximum lock count exceeded");
            // 设置状态
            setState(nextc);
            return true;
        }
        return false;
    }
}
```
### 2.2 说明: 

#### 1）与非公平锁的最大区别：

在使用CAS设置尝试设置state值前，调用了`hasQueuedPredecessors()`判断同步队列是否存在结点，如果存在必须先执行完同步队列中结点的线程，当前线程进入等待状态



总是先取队列前面的线程进行加锁

- 跟踪lock方法的源码可知，当资源空闲时，它总是会先判断sync队列(AbstractQueuedSynchronizer中的数据结构)是否有等待时间更长的线程（没有比我优先级更高的线程在请求锁了），如果存在，则将该线程加入到等待队列的尾部，实现了公平获取原则。其中，FairSync类的lock的方法调用如下，只给出了主要的方法。

![image-20200727123825742](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165555.png)

- 可以看出只要资源被其他线程占用，该线程就会添加到sync queue中的尾部，而不会先尝试获取资源。这也是和Nonfair最大的区别，Nonfair每一次都会尝试去获取资源，如果此时该资源恰好被释放，则会被当前线程获取，这就造成了不公平的现象，当获取不成功，再加入队列尾部。



## 参考文章

https://blog.csdn.net/lipeng_bigdata/article/details/52154637<br>
https://www.pdai.tech/md/java/thread/java-thread-x-lock-ReentrantLock.html