# ThreadPoolExecutor

# 一、基本介绍

## 1、概念

　java.uitl.concurrent.ThreadPoolExecutor类是线程池中最核心的一个实现类，最终实现了ExecutorService接口

## 2、构造方法

```java
public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
                          BlockingQueue<Runnable> workQueue);

public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory);

public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,RejectedExecutionHandler handler);

public ThreadPoolExecutor(int corePoolSize,int maximumPoolSize,long keepAliveTime,TimeUnit unit,
        BlockingQueue<Runnable> workQueue,ThreadFactory threadFactory,RejectedExecutionHandler handler);
```

## 3、参数7个（常用前面三个）

### 1）corePoolSize：核心线程数

核心池的大小，线程池的基本大小，即在**没有任务需要执行的时候线程池的大小**，并且只有在工作队列满了的情况下才会创建超出这个数量的线程。

在创建了线程池后，默认情况下，线程池中并没有任何线程，而是等待有任务到来才创建线程去执行任务，除非调用了prestartAllCoreThreads()或者prestartCoreThread()方法，从这2个方法的名字就可以看出，是预创建线程的意思，即在没有任务到来之前就创建corePoolSize个线程或者一个线程。

默认情况下，在创建了线程池后，线程池中的线程数为0，当有任务来之后，就会创建一个线程去执行任务，当线程池中的线程数目达到corePoolSize后，就会把到达的任务放到缓存队列当中；

### 2）maximumPoolSize：最大线程数

线程池最大线程数，它表示在线程池中最多能创建多少个线程；

### 3）workQueue：阻塞队列

一个阻塞队列，用来存储等待执行的任务，这个参数的选择会对线程池的运行过程产生重大影响，一般来说，这里的阻塞队列有以下几种选择：

ArrayBlockingQueue; LinkedBlockingQueue; SynchronousQueue;



### 4）TimeUnit：保留时间单位

参数keepAliveTime的时间单位，有7种取值，在TimeUnit类中有7种静态属性，以及TimeUtil的源码展示：

```java
TimeUnit.DAYS;               //天
TimeUnit.HOURS;             //小时
TimeUnit.MINUTES;           //分钟
TimeUnit.SECONDS;           //秒
TimeUnit.MILLISECONDS;      //毫秒
TimeUnit.MICROSECONDS;      //微妙
TimeUnit.NANOSECONDS;       //纳秒
```

### 5）keepAliveTime：线程空闲时间

表示线程没有任务执行时最多保持多久时间会终止。默认情况下，只有当线程池中的线程数大于corePoolSize时，keepAliveTime才会起作用，直到线程池中的线程数不大于corePoolSize：即当线程池中的线程数大于corePoolSize时，如果一个线程空闲的时间达到keepAliveTime，则会终止，直到线程池中的线程数不超过corePoolSize；但是如果调用了allowCoreThreadTimeOut(boolean)方法，在线程池中的线程数不大于corePoolSize时，keepAliveTime参数也会起作用，直到线程池中的线程数为0；

### 6）ThreadFactory：线程工厂，用来创建线程

### 7）handler：异常处理策略（饱和策略）

队列已满，而且任务量大于最大线程的异常处理策略

有以下取值：

- ThreadPoolExecutor.AbortPolicy ： 拒绝新任务的处理并抛出RejectedExecutionException异常。 
- ThreadPoolExecutor.DiscardPolicy ：不处理新任务，直接丢弃掉。，但是不抛出异常。 
- ThreadPoolExecutor.DiscardOldestPolicy  ：丢弃队列最前面（最早的未处理）的任务，然后重新尝试执行任务（重复此过程）
- ThreadPoolExecutor.CallerRunsPolicy		  //由调用线程处理该任务 





## 4、方法

```
execute()
submit()
shutdown()
shutdownNow()    
getQueue() 
getPoolSize() 
getActiveCount()
getCompletedTaskCount()
```

### 4.1 execute() vs submit()

- execute()方法**用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否；**
  - 实际上是Executor中声明的方法，在ThreadPoolExecutor进行了具体的实现，这个方法是ThreadPoolExecutor的核心方法，通过这个方法可以向线程池提交一个任务，交由线程池去执行。
- submit()方法**用于提交需要返回值的任务**。线程池会返回一个 **Future** 类型的对象，通过这个 Future 对象可以判断任务是否执行成功，并且可以通过 Future 的 get()方法来获取**返回值**，get()方法会阻塞当前线程直到任务完成，而使用 get（long timeout，TimeUnit unit）方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。
  - submit()方法是在ExecutorService中声明的方法，在AbstractExecutorService就已经有了具体的实现，在ThreadPoolExecutor中并没有对其进行重写



shutdown()和shutdownNow()是用来关闭线程池的。

getQueue() 、getPoolSize() 、getActiveCount()、getCompletedTaskCount()等获取与线程池相关属性的方法

还有在下文提到的runwork()、addwork()、processworkerExit() 方法等等

### 4.2 Shutdown() 和shutdownNow()

- shutdown（） :关闭线程池，线程池的状态变为 SHUTDOWN。线程池不再接受新任务了，但是队列里的任务得执行完毕。
- shutdownNow（） :关闭线程池，线程的状态变为 STOP。线程池会终止当前正在运行的任务，并停止处理排队的任务并返回正在等待执行的 List。

#### 4.3.2 isTerminated() VS isShutdown()

- isShutDown 当调用 shutdown() 方法后返回为 true。
- isTerminated 当调用 shutdown() 方法后，并且所有提交的任务完成后返回为 true

### 



# 二、底层实现

## 1、执行过程：excute( )

一个任务从提交到执行完毕经历过程如下：

- 如果当前（核心）线程池中的线程数目 < corePoolSize，则每来一个任务，就会创建一个线程去执行这个任务；

- 如果当前（核心）线程池中的线程数目 >= corePoolSize，则每来一个任务，会尝试将其添加到任务缓存队列当中：
  - 若任务缓存队列还没满，则添加成功，则该任务会等待空闲线程将其取出去执行；
  - 若任务缓存队列已满，则会去判断线程池数量是否已满；
- 如果缓冲队列workQueue已满（而且如果当前（核心）线程池中的线程数目 >= corePoolSize）
  - 如果当前线程池中的线程数量 < maximumPoolSize，则会创建新的线程来处理被添加的任务
  - 如果当前线程池中的线程数目 >= maximumPoolSize，则会采取任务拒绝策略进行处理；

```java
// 存放线程池的运行状态 (runState) 和线程池内有效线程的数量 (workerCount)
private final AtomicInteger ctl = new AtomicInteger(ctlOf(RUNNING, 0));
   
private static int workerCountOf(int c) {
    return c & CAPACITY;
}
    //任务队列
private final BlockingQueue<Runnable> workQueue;

public void execute(Runnable command) {
        // 如果任务为null，则抛出异常。
    if (command == null)
        throw new NullPointerException();
        // ctl 中保存的线程池当前的一些状态信息
    int c = ctl.get();

        //  下面会涉及到 3 步 操作
        // 1.首先判断当前线程池中之行的任务数量是否小于 corePoolSize
        // 如果小于的话，通过addWorker(command, true)新建一个线程，并将任务(command)添加到该线程中；然后，启动该线程从而执行任务。
    if (workerCountOf(c) < corePoolSize) {
        if (addWorker(command, true))
            return;
        c = ctl.get();
    }
        // 2.如果当前之行的任务数量大于等于 corePoolSize 的时候就会走到这里
        // 通过 isRunning 方法判断线程池状态，线程池处于 RUNNING 状态才会被并且队列可以加入任务，该任务才会被加入进去
    if (isRunning(c) && workQueue.offer(command)) {
        int recheck = ctl.get();
            // 再次获取线程池状态，如果线程池状态不是 RUNNING 状态就需要从任务队列中移除任务，并尝试判断线程是否全部执行完毕。同时执行拒绝策略。
        if (!isRunning(recheck) && remove(command))
            reject(command);
        // 如果当前线程池为空就新创建一个线程并执行。
        else if (workerCountOf(recheck) == 0)
            addWorker(null, false);
    	}
        //3. 通过addWorker(command, false)新建一个线程，并将任务(command)添加到该线程中；然后，启动该线程从而执行任务。
        //如果addWorker(command, false)执行失败，则通过reject()执行相应的拒绝策略的内容。
        else if (!addWorker(command, false))
            reject(command);
    }
```

addWorker 这个方法主要用来创建新的工作线程，如果返回true说明创建和启动工作线程成功，否则的话返回的就是false。





# 三、参数

## 2、任务拒绝策略：handler

当线程池的任务缓存队列已满并且线程池中的线程数目达到maximumPoolSize，如果还有任务到来就会采取任务拒绝策略，通常有以下四种策略：

- ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。
- ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
- ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
- ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务



## 3、阻塞队列:BlockingQueue

### 3.1  两种常见的阻塞场景：

- 当队列中没有数据的情况下，消费者端的所有线程都会被自动阻塞（挂起），直到有数据放入队列。
- 当队列中填满数据的情况下，生产者端的所有线程都会被自动阻塞（挂起），直到队列中有空的位置，线程被自动唤醒。

阻塞队列常用于生产者和消费者的场景，生产者是向队列里添加元素的线程，消费者是从队列里取元素的线程。阻塞队列就是生产者用来存放元素、消费者用来获取元素的容器。

### 3.2  常见的7种BlockingQueue

**ArrayBlockingQueue：**基于数组的有界阻塞队列实现。生产者放入数据和消费者获取数据，共用同一个锁对象。默认采用非公平锁。

**LinkedBlockingQueue：**基于链表的有界阻塞队列。生产者端和消费者端分别采用了独立的锁来控制数据同步，并发性能较好。需要注意的是，LinkedBlockingQueue会默认一个类似无限大小的容量（Integer.MAX_VALUE）。

**PriorityBlockingQueue：** 基于优先级的阻塞无界队列（优先级的判断通过构造函数传入的Compator对象来决定，但不保证同优先级元素顺序）。不会阻塞数据生产者，而只会在没有可消费的数据时，阻塞数据的消费者。内部控制线程同步的锁采用的是公平锁。

**SynchronousQueue**：无缓冲的等待队列。每个插入操作必须等待另一个线程的移除操作，同样任何一个移除操作都要等待另一个线程的插入操作。由于队列没有容量，所以不能调用peek操作（返回队列头元素）。

**DelayQueue**：支持延时获取元素的无界阻塞队列。队列中每个元素必须实现Delayed接口。插入数据的操作（生产者）永远不会被阻塞，只有获取数据的操作（消费者）才会被阻塞。

**LinkedTransferQueue**：一个由链表结构组成的无界阻塞队列。

**LinkedBlockingDeque**：一个由链表结构组成的双向阻塞队列





### 3.3  常用方法

| 方法 | 抛出异常 | 返回特殊值 | 一直阻塞 | 超时退出               |
| ---- | -------- | ---------- | -------- | ---------------------- |
| 插入 | add（e） | offer      | put      | offer（e，time，unit） |
| 移除 | remove   | poll       | take     | poll（time，unit）     |
| 检查 | element  | peek       | 不可用   | 不可用                 |

put方法用来向队尾存入元素，如果队列满，则等待；

take方法用来从队首取元素，如果队列为空，则等待；

offer方法用来向队尾存入元素，如果队列满，则等待一定的时间，当时间期限达到时，如果还没有插入成功，则返回false；否则返回true；

poll方法用来从队首取元素，如果队列空，则等待一定的时间，当时间期限达到时，如果取到，则返回null；否则返回取得的元素；



**注意**：如果是无界阻塞队列，队列不可能会出现满的情况，所以使用put或offer方法永远不会被阻塞，而且使用offer方法时，该方法永远返回true













# 参考文章

[Java线程池的底层实现与使用](https://www.cnblogs.com/sxkgeek/p/9343519.html)

[Java阻塞队列](https://www.jianshu.com/p/32665a52eba1)

[JUC线程池源码分析](http://www.throwable.club/2019/07/15/java-concurrency-thread-pool-executor/)



