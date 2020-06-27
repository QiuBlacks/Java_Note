[TOC]

# 线程同步（synchronized&ReentrantLock）

## 一、基本问题

### 1、问题引出

在多线程编程中，有可能会出现同时访问同一个资源的情况，这种资源可以是各种类型的的资源，由于每个线程执行的过程是不可控的，所以很可能导致最终的结果与实际上的愿望相违背或者直接导致程序出错。

### 2、解决方法

Java 提供了两种锁机制来控制多个线程对共享资源的互斥访问，第一个是 JVM 实现的 synchronized，而另一个是 JDK 实现的 ReentrantLock。

### 3、二者比较

#### 1）锁的实现

synchronized 是 JVM 实现的，而 ReentrantLock 是 JDK 实现的。

#### 2） 性能

新版本 Java 对 synchronized 进行了很多优化，例如自旋锁等，synchronized 与 ReentrantLock 大致相同。

#### 3） 等待可中断

当持有锁的线程长期不释放锁的时候，正在等待的线程可以选择放弃等待，改为处理其他事情。

ReentrantLock 可中断，而 synchronized 不行。

#### 4.）公平锁

公平锁是指多个线程在等待同一个锁时，必须按照申请锁的时间顺序来依次获得锁。

synchronized 中的锁是非公平的，ReentrantLock 默认情况下也是非公平的，但是也可以是公平的。

#### 5.）锁绑定多个条件

一个 ReentrantLock 可以同时绑定多个 Condition 对象。

### 4、使用选择

除非需要使用 ReentrantLock 的高级功能，否则优先使用 synchronized。这是因为 synchronized 是 JVM 实现的一种锁机制，JVM 原生地支持它，而 ReentrantLock 不是所有的 JDK 版本都支持。并且使用 synchronized 不用担心没有释放锁而导致死锁问题，因为 JVM 会确保锁的释放。





## 二、synchronized

### 1、同步代码块

#### 1）基本步骤

```java
synchronized （同步监视器、锁）{    
    //操作共享数据的代码    
    //需要被同步的代码
}
```



#### 2）实例

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

### 2、同步方法 

#### 2.1 实例方法	

对象锁是当前对象  即 this 

```Java
//实例方法的对象锁是当前对象  即 this   
public  synchronized void method(){   }
```

   

#### 2.2 静态方法

​		对象锁为当前类   .class

​	然后可以进行this.notify()，类.class.notify（）

尽量不要使用 synchronized(String a) 因为JVM中，字符串常量池具有缓存功能！

```java
//静态方法的对象锁为当前类.class 
public static synchronized void method(){   }
```

#### 2.3  实例

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

### 3、synchronized优化

在同一时刻只有一个线程能够获得对象的监视器（monitor），从而进入到同步代码块或者同步方法之中，即表现为互斥性（排它性）。这种方式肯定效率低下

让每次通过的速度变快一点







------

## 三、ReentrantLock

### 1、基本介绍

ReentrantLock 是 java.util.concurrent（J.U.C）包中的锁

### 2、Lock

lock(); 获取锁

unlock(); 释放锁

```java
 lock(); 获取锁
try{
   操作共享数据的代码
  } finally {
   //释放锁
   unlock();
  }
```

实例：

```java
  public void set(String name){  
            lock.lock();//锁住此语句与lock.unlock()之间的代码  
            try{  
                while(flag)  
                    condition_pro.await(); //生产者线程在conndition_pro对象上等待  
                this.name=name+"---"+count++;  
                System.out.println(Thread.currentThread().getName()+"...生产者..."+this.name);  
                flag=true;  
                 condition_con.signalAll();  
            }  
            finally{  
                lock.unlock(); //unlock()要放在finally块中。  
            }  
        }  
```





## 四、谈谈 synchronized和ReentrantLock 的区别

### 1、 两者都是可重入锁

两者都是可重入锁。“可重入锁”概念是：自己可以再次获取自己的内部锁。比如一个线程获得了某个对象的锁，此时这个对象锁还没有释放，当其再次想要获取这个对象的锁的时候还是可以获取的，如果不可锁重入的话，就会造成死锁。同一个线程每次获取锁，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

### 2、synchronized 依赖于 JVM 而 ReentrantLock 依赖于 API

synchronized 是依赖于 JVM 实现的，前面我们也讲到了 虚拟机团队在 JDK1.6 为 synchronized 关键字进行了很多优化，但是这些优化都是在虚拟机层面实现的，并没有直接暴露给我们。ReentrantLock 是 JDK 层面实现的（也就是 API 层面，需要 lock() 和 unlock() 方法配合 try/finally 语句块来完成），所以我们可以通过查看它的源代码，来看它是如何实现的。

### 3、ReentrantLock 比 synchronized 增加了一些高级功能

相比synchronized，ReentrantLock增加了一些高级功能。

主要来说主要有三点：

#### 3.1等待可中断；

  ReentrantLock**提供了一种能够中断等待锁的线程的机制**，通过lock.lockInterruptibly()来实现这个机制。也就是说正在等待的线程可以选择放弃等待，改为处理其他事情。

#### 3.2    可实现公平锁

ReentrantLock**可以指定是公平锁还是非公平锁**。而synchronized只能是非公平锁。所谓的公平锁就是先等待的线程先获得锁。 ReentrantLock默认情况是非公平的，可以通过 ReentrantLock类的`ReentrantLock(boolean fair)`构造方法来制定是否是公平的。

#### 3.3  可实现选择性通知（锁可以绑定多个条件）

synchronized关键字与wait()和notify()/notifyAll()方法相结合可以实现等待/通知机制，ReentrantLock类当然也可以实现，但是需要借助于Condition接口与newCondition() 方法。

Condition是JDK1.5之后才有的，它具有很好的灵活性，比如可以实现多路通知功能也就是在一个Lock对象中可以创建多个Condition实例（即对象监视器），**线程对象可以注册在指定的Condition中，从而可以有选择性的进行线程通知，在调度线程上更加灵活。 在使用notify()/notifyAll()方法进行通知时，被通知的线程是由 JVM 选择的，用ReentrantLock类结合Condition实例可以实现“选择性通知”** ，这个功能非常重要，而且是Condition接口默认提供的。

而synchronized关键字就相当于整个Lock对象中只有一个Condition实例，所有的线程都注册在它一个身上。如果执行notifyAll()方法的话就会通知所有处于等待状态的线程这样会造成很大的效率问题，而Condition实例的signalAll()方法 只会唤醒注册在该Condition实例中的所有等待线程。



如果你想使用上述功能，那么选择ReentrantLock是一个不错的选择。

### 4、性能已不是选择标准