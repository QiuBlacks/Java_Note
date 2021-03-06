# CMS收集器（Concurrent Mark Sweep）

### 1、介绍

是一种以获取**最短回收暂停时间**为目标的收集器，它是基于“标记-清除”算法实现的，并且常见的应用场景是互联网站或者B/S系统的服务端上的Java应用



### 2、算法过程

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910165305.png" alt="image-20200615204106693" style="zoom: 67%;" />



- ==初始标记==：程序所有的工作线程被STW所暂停，==标记GC Roots能直接关联到的对象==，一旦标记完成之后就会恢复之前被暂停的所有应用线程。由于直接关联对象比较小，因此速度很快，需要**“Stop The World”。**

- ==并发标记==：从GC Roots的**直接关联对象**开始遍历整个对象图的过程，在整个过程中耗时最长但不需要暂停应用线程，它们可以和用户线程**并发执行**  

-  并发预处理：工作还是标记，与重标记功能相似。重标记需要STW（Stop The World），因此重标记的工作尽可能多的在并发阶段完成来减少STW的时间。

   此阶段标记从新生代晋升的对象、新分配到老年代的对象以及在并发阶段被修改了的对象。

- ==重标记==：为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录，这个阶段的停顿时间一般会比初始标记阶段稍长一些，但远比并发标记的时间短。此阶段也需要**“Stop The World”。**

- ==并发清除==：清除标记阶段判断已经死亡的对象，并释放内存空间，它可以和用户线程同时**并发执行**

- 重置： CMS清除内部状态，为下次回收做准备

初始标记和重新标记的存在，使得CMS仍在存在STW。但并发标记和并发清除阶段不需要暂停其他的用户线程，因此整体的回收是低停顿的。

由于垃圾回收阶段用户线程并没有中断，所以在CMS回收过程中换应该确保用户线程有足够的内存可用。因此，CMS不是当老年代完全没有空间可用时才被触发，而是当**堆内存的使用率达到一个阈值**时，便开始进行回收，确保应用程序在CMS工作过程中依然有足够的空间支持应用程序运行。

如**果CMS运行期间预留的内存空间不够用户程序使用，那么就会出现一次"Concurrent Mode Failure"失败**，此时虚拟机将会临时启用**Serial Old**作为老年代的垃圾回收器使用，这样停顿时间就长了。



### 3、优点和缺陷

#### 3.1 优点：

- 并发收集

- 低延迟

  

#### 3.2 缺陷：

##### 1）垃圾碎片的问题

​	我们都知道CMS是使用的是标记-清除算法的，所以不可避免的就是会出现垃圾碎片的问题

##### 2）remark阶段停顿时间长

一般CMS的GC耗时80%都在remark阶段，remark阶段停顿时间会很长，在CMS的这四个主要的阶段中，最费时间的就是重新标记阶段

##### 3）concurrent mode failure

这个异常发生在cms正在回收的时候。执行CMS GC的过程中，由于垃圾回收阶段用户线程仍在执行，必需预留出内存空间给用户线程使用。

因此不能像其他回收器那样，等到老年代满了再进行GC。当年轻代空间满了，执行ygc时，需要将存活的对象放入到老年代，而此时老年代空间不足，这时CMS还没有机会回收老年带产生的，或者在做Minor GC的时候，新生代救助空间放不下，需要放入老年代，而老年代也放不下而产生的

##### 4）promotion failed：

这个问题是指，在进行Minor GC时，Survivor空间不足，对象只能放入老年代，而此时老年代也放不下造成的，多数是由于老年代有足够的空闲空间，但是由于碎片较多，新生代要转移到老年带的对象比较大,找不到一段连续区域存放这个对象导致的。



### 4、解决方法

#### 1）垃圾碎片的问题

针对这个问题，这时候我们需要用到这个参数：`-XX:CMSFullGCsBeforeCompaction=n` 意思是说在上一次CMS并发GC执行过后，到底还要再执行多少次不压缩的`full GC` 后才会做一次压缩。

默认是0，也就是在默认配置下每次CMS GC顶不住了而要转入full GC的时候都会做压缩。



#### 2）remark阶段停顿时间会很长的问题：

解决这个问题巨简单，加入`-XX:+CMSScavengeBeforeRemark`。在执行remark操作之前先做一次`Young GC`，目的在于减少年轻代对老年代的无效引用，降低remark时的开销。



#### 3）concurrent mode failure

解决这个问题其实很简单，只需要设置两个参数即可

`-XX:+UseCMSInitiatingOccupancyOnly `：CMS会根据历史记录，预测老年代还需要多久填满及进行一次回收所需要的时间。在老年代空间用完之前，CMS可以根据自己的预测自动执行垃圾回收。

`-XX:CMSInitiatingOccupancyFraction=60`：是指设定CMS在对内存占用率达到60%的时候开始GC。

为什么设置这两个参数呢？由于在垃圾收集阶段用户线程还需要运行，那也就还需要预留有足够的内存空间给用户线程使用，因此CMS收集器不能像其他收集器那样等到老年代几乎完全被填满了再进行收集。

当然也不能设置过高，比如90%，这时候虽然GC次数少，但是，却会导致用于用户线程空间小，效率不高，太低10%，你自己想想会怎么样，体会体会！



###  5、相关参数

```yaml
-XX:UseConcMarkSweepGC：手动指定使用CMS，此时的组合为ParNew+CMS+Serial OLD（后备方案）
-XX:CMSInitiatingOccupanyFraction：设置堆内存使用率的阈值，一旦达到该阈值，并开始进行回收，默认值为68
-XX:+UseCMSCompactAtFullCollection：用于指定在执行完Full GC后堆内存空间是否进行压缩整理。避免内存碎片的产生，它会导致停顿时间变长
-XX:CMSFullGCsBefoerCompaction：设置在执行完多少次Full GC后对内存空间进行压缩整理
-XX:ParallelCMSThreads：设置CMS的线程数，默认是（ParallelGCThreads + 3） / 4
避免内存碎片的产生，它会导致停顿时间变长

-XX:CMSFullGCsBefoerCompaction：设置在执行完多少次Full GC后对内存空间进行压缩整理

-XX:ParallelCMSThreads：设置CMS的线程数，默认是（ParallelGCThreads + 3） / 4
```



## 参考文章

[Java虚拟机 -- 垃圾回收器（上篇）](https://blog.csdn.net/Forlogen/article/details/106724160)

