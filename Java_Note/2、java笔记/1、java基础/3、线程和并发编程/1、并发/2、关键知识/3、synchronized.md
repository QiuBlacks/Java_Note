# synchronized

# 一、基本问题

## 1、问题引出

在多线程编程中，有可能会出现同时访问同一个资源的情况，这种资源可以是各种类型的的资源，由于每个线程执行的过程是不可控的，所以很可能导致最终的结果与实际上的愿望相违背或者直接导致程序出错。

## 2、解决方法

Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。

## 3、synchronized介绍

synchronized 关键字解决的是多个线程之间访问资源的同步性，synchronized关键字可以保证被它修饰的方法或者代码块在任意时刻只能有一个线程执行。

## 4、优化

在 Java 早期版本中，synchronized 属于 **重量级锁**，效率低下

因为监视器锁（monitor）是依赖于底层的操作系统的 `Mutex Lock` 来实现的，Java 的线程是映射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比较长的时间，时间成本相对较高。

在 Java 6 之后 Java 官方对从 JVM 层面对 synchronized 较大优化，所以现在的 synchronized 锁效率也优化得很不错了。JDK1.6 对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。

所以，你会发现目前的话，不论是各种开源框架还是 JDK 源码都大量使用了 synchronized 关键字。





# 二、synchronized

# 1、使用方法

## 1.1、修饰同步代码块

指定加锁对象，对**给定对象/类**加锁。

- synchronized(this|object) 表示进入同步代码库前要获得给定对象的锁

- synchronized(类.class) 表示进入同步代码前要获得 当前 class 的锁



### 1）基本步骤

```java
synchronized （同步监视器、锁）{    
    //操作共享数据的代码    
    //需要被同步的代码
}
```

### 2）实例

```java
public class text {
        public static void main(String[] args) {
            MyThread a = new MyThread("A");
            MyThread b = new MyThread("B");
            MyThread c = new MyThread("C");
            a.start();
            b.start();
            c.start();
        }
    public static class MyThread extends Thread {
        private static int count = 10;
        public MyThread(String name) {
            super();
            this.setName(name);
        }

    public void run() {
        super.run();
        while (count > 0) {  //B,C会在此等待，等A结束会继续运行while里的
            //当票数为1时，若A抢到资源
            if(count<0) return;
            synchronized (MyThread.class) {
            System.out.println("由 " + MyThread.currentThread().getName()
                    + " 计算，count=" + count--);
}
```

注意：Runable天生共享锁，而Thread中需要用static对象或者this关键字或者当前类（window。class）来充当唯一锁



## 1.2、修饰同步方法 

### 1) 实例方法	

对象锁是当前对象实例  即 this 

```Java
//实例方法的对象锁是当前对象  即 this   
public  synchronized void method(){   }
```

   

### 2) 静态方法

也就是给**当前类**加锁，会作用于**当前类的所有对象实例** ，进入同步代码前要获得 **当前 class 的锁**。



然后可以进行this.notify()，类.class.notify（）

尽量不要使用 synchronized(String a) 因为JVM中，字符串常量池具有缓存功能！

```java
//静态方法的对象锁为当前类.class 
public static synchronized void method(){   }
```

**注意**：因为静态成员不属于任何一个实例对象，是类成员（ static 表明这是该类的一个静态资源，不管 new 了多少个对象，**只有一份**）。所以，如果一个线程 A 调用一个实例对象的非静态 synchronized 方法，而线程 B 需要调用这个实例对象所属类的静态 synchronized 方法，**是允许的，不会发生互斥现象**。

因为访问静态 synchronized 方法占用的锁是当前类的锁，而访问非静态 synchronized 方法占用的锁是当前实例对象锁。



### 3)  实例

把上面的公共代码提取出来

```java
private static synchronized  void sold(){
    System.out.println("由 " + MyThread.currentThread().getName()
            + " 计算，count=" + count--);
      //将该锁唤醒
       MyThread.class.notify();
       //等待
       try {
            MyThread.class.wait();
            } catch (InterruptedException e) {
            e.printStackTrace();
        }
}
```









# 三、底层实现

## 1、基础知识

Java 虚拟机中的同步(Synchronization)基于进入和退出**管程(Monitor)**对象实现

在JVM中，对象在内存中的布局分为三块区域：**对象头、实例数据和对齐填充**

### 1.1 对象头

实现synchronized的锁对象的基础

主要结构是由Mark Word 和 Class Metadata Address 组成，其结构说明如下表：

| 虚拟机位数 | 头对象结构             | 说明                                                         |
| ---------- | ---------------------- | ------------------------------------------------------------ |
| 32/64bit   | Mark Word              | 存储对象的hashCode、锁信息或分代年龄或GC标志等信息           |
| 32/64bit   | Class Metadata Address | 类型指针指向对象的类元数据，JVM通过这个指针确定该对象是哪个类的实例。 |

其中Mark Word在默认情况下存储着对象的HashCode、分代年龄、锁标记位等以下是32位JVM的Mark Word默认存储结构

| 锁状态   | 25bit        | 4bit         | 1bit是否是偏向锁 | 2bit 锁标志位 |
| -------- | ------------ | ------------ | ---------------- | ------------- |
| 无锁状态 | 对象HashCode | 对象分代年龄 | 0                | 01            |

由于对象头的信息是与对象自身定义的数据没有关系的额外存储成本，因此考虑到JVM的空间效率，Mark Word 被设计成为一个非固定的数据结构，以便存储更多有效的数据，它会根据对象本身的状态复用自己的存储空间



### 1.2 实例变量

存放类的属性数据信息，包括父类的属性信息，如果是数组的实例部分还包括数组的长度，这部分内存按4字节对齐。

### 1.3填充数据

由于虚拟机要求对象起始地址必须是8字节的整数倍。填充数据不是必须存在的，仅仅是为了字节对齐，这点了解即可。



## 2、实现基础：获取Monitor

32位JVM下，Mark Word的存储结构：

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201005104548.png)

我们主要分析一下重量级锁也就是通常说synchronized的对象锁，锁标识位为10，其中指针指向的是**monitor对象（也称为管程或监视器锁）**的起始地址。

**每个对象都存在着一个 monitor 与之关联**，对象与其 monitor 之间的关系有存在多种实现方式，如monitor可以与对象一起创建销毁或当线程试图获取对象锁时自动生成，**但当一个 monitor 被某个线程持有后，它便处于锁定状态**。



### 2.1 实现基础的原理

在Java虚拟机(HotSpot)中，**monitor是由ObjectMonitor实现**的。ObjectMonitor中有两个队列，**WaitSet 和 _EntryList**，用来保存ObjectWaiter对象列表( 每个等待锁的线程都会被封装成ObjectWaiter对象)，owner指向持有ObjectMonitor对象的线程。

当多个线程同时访问一段同步代码时，首先会进入 _EntryList 集合，当线程获取到对象的monitor 后进入 _Owner 区域并把monitor中的owner变量设置为当前线程同时monitor中的计数器count加1，若线程调用 wait() 方法，将释放当前持有的monitor，owner变量恢复为null，count自减1，同时该线程进入 WaitSe t集合中等待被唤醒。若当前线程执行完毕也将释放monitor(锁)并复位变量的值，以便其他线程进入获取monitor(锁)。如下图所示：

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201005105210.png)

由此看来，monitor对象存在于每个Java对象的对象头中(存储的指针的指向)，synchronized锁便是通过这种方式获取锁的，也是为什么Java中任意对象可以作为锁的原因，同时也是notify/notifyAll/wait等方法存在于顶级对象Object中的原因



## 3、synchronized底层原理

### 3.1 修饰代码块

示例：

```
public class SyncCodeBlock {

   public int i;

   public void syncTask(){
       //同步代码库
       synchronized (this){
           i++;
       }
   }
}
```

编译上述代码并使用javap反编译后得到字节码：

```java
 //===========主要看看syncTask方法实现================
  public void syncTask();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=3, locals=3, args_size=1
         //..........省略其他  
         3: monitorenter  //注意此处，进入同步方法
          //..........省略其他  
        15: monitorexit   //注意此处，退出同步方法
        16: goto    
    	 //..........省略其他  
        21: monitorexit //注意此处，退出同步方法
            
      //省略其他字节码.......
}
```

从字节码中可知同步语句块的实现使用的是monitorenter 和 monitorexit 指令，其中monitorenter指令指向同步代码块的开始位置，monitorexit指令则指明同步代码块的结束位置。

当执行monitorenter指令时，**当前线程将试图获取 objectref(即对象锁) 所对应的 monitor 的持有权**，当 objectref 的 monitor 的进入计数器为 0，那线程可以成功取得 monitor，并将计数器值设置为 1，取锁成功。

**如果当前线程已经拥有 objectref 的 monitor 的持有权，**那它可以重入这个 monitor (关于重入性稍后会分析)，重入时计数器的值也会加 1。倘若其他线程已经拥有 objectref 的 monitor 的所有权，那当前线程将被阻塞，直到正在执行线程执行完毕，即monitorexit指令被执行，执行线程将释放 monitor(锁)并设置计数器值为0 ，其他线程将有机会持有 monitor 。

值得注意的是编译器将会确保无论方法通过何种方式完成，方法中调用过的每条 monitorenter 指令都有执行其对应 monitorexit 指令，而无论这个方法是**正常结束还是异常结束**。为了保证在方法异常完成时 monitorenter 和 monitorexit 指令依然可以正确配对执行，编译器会自动产生一个异常处理器，这个异常处理器声明可处理所有的异常，它的目的就是用来执行 monitorexit 指令。从字节码中也可以看出多了一个monitorexit指令，**它就是异常结束时被执行的释放monitor 的指令。**



### 3.2 修饰方法

JVM可以从方法常量池中的方法表结构(method_info Structure) 中的 **ACC_SYNCHRONIZED** 访问标志区分一个方法是否同步方法。当方法调用时，调用指令将会 检查方法的 ACC_SYNCHRONIZED 访问标志是否被设置，**如果设置了，执行线程将先持有monitor**（虚拟机规范中用的是管程一词）， 然后再执行方法，最后再方法完成(无论是正常完成还是非正常完成)时释放monitor。在方法执行期间，执行线程持有了monitor，其他任何线程都无法再获得同一个monitor。如果一个同步方法执行期间抛 出了异常，并且在方法内部无法处理此异常，那这个同步方法所持有的monitor将在异常抛到同步方法之外时自动释放。

下面我们看看字节码层面如何实现：

```
public class SyncMethod {

   public int i;

   public synchronized void syncTask(){
           i++;
   }
}
```

使用javap反编译后的字节码如下：

```java
 //==================syncTask方法======================
  public synchronized void syncTask();
    descriptor: ()V
    //方法标识ACC_PUBLIC代表public修饰，ACC_SYNCHRONIZED指明该方法为同步方法
    flags: ACC_PUBLIC, ACC_SYNCHRONIZED
    Code:
      stack=3, locals=1, args_size=1
         0: aload_0
         1: dup
         2: getfield      #2                  // Field i:I
         5: iconst_1
         6: iadd
         7: putfield      #2                  // Field i:I
        10: return
      LineNumberTable:
        line 12: 0
        line 13: 10
}
```





# 四、常见问题

## 1、构造方法能不能使用 synchronized 关键字修饰

构造方法不能使用 synchronized 关键字修饰

构造方法本身就属于线程安全的，不存在同步的构造方法一说。









# 五、谈谈 synchronized和ReentrantLock 的区别

### 1、 两者都是可重入锁

“可重入锁”概念是：自己可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。同一个线程每次获取锁，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

## 2、synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

synchronized 是依赖于 JVM 实现的，前面我们也讲到了 虚拟机团队在 JDK1.6 为 synchronized 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。

ReentrantLock 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。

## 3、ReentrantLock 比 synchronized 增加了一些高级功能

相比synchronized，ReentrantLock增加了一些高级功能。

主要来说主要有三点：

### 3.1等待可中断；

  ReentrantLock**提供了一种能够中断等待锁的线程的机制**，通过lock.lockInterruptibly()来实现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情，而 synchronized 不行。

### 3.2    可实现公平锁

ReentrantLock**可以指定是公平锁还是非公平锁**。而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。 ReentrantLock默认情况是非公平的，可以通过 ReentrantLock类的`ReentrantLock(boolean fair)`构造方法来制定是否是公平的。

### 3.3  可实现选择性通知（锁可以绑定多个条件）

synchronized关键字与wait()和notify()/notifyAll()方法相结合可以实现等待/通知机制，ReentrantLock类当然也可以实现，但是需要借助于Condition接口与newCondition() 方法。

Condition是JDK1.5之后才有的，它具有很好的灵活性，比如可以实现多路通知功能也就是在一个Lock对象中可以创建多个Condition实例（即对象监视器），**线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。 在使用notify()/notifyAll()方法进行通知时，被通知的线程是由 JVM 选择的，用ReentrantLock类结合Condition实例可以实现“选择性通知”** ，这个功能非常重要，而且是Condition接口默认提供的。

而synchronized关键字就相当于整个Lock对象中只有一个Condition实例，所有的线程都注册在它一个身上。如果执行notifyAll()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题，而Condition实例的signalAll()方法 只会唤醒注册在该Condition实例中的所有等待线程。



如果你想使用上述功能，那么选择ReentrantLock是一个不错的选择。

## 4、性能已不是选择标准

## 4、使用选择

除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。

这是因为 synchronized 是 JVM 实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。并且使用 synchronized 不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。

- Java 6 开始，ReenstrantLock 和内置锁的性能相差不大；
- synchronized 是 JVM 的内置属性，未来更有可能对 synchronized 进行性能优化，如对线程封闭的锁对象的锁消除，增加锁的粒度等；
- ReenstrantLock 危险性更高（如忘记在 finally 块中 lock.unlock() 了，会导致锁永远无法被释放，出现问题，极难 debug）；
- 许多现有程序中已使用了 synchronized，两种方式混合使用比较易错。





## 参考文章

[深入理解Java并发之synchronized实现原理](https://blog.csdn.net/javazejian/article/details/72828483#synchronized%E5%90%8C%E6%AD%A5%E4%BB%A3%E7%A0%81%E5%9D%97)

