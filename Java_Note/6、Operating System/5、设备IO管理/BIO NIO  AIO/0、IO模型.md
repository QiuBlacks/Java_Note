[TOC]



# I/O模型

---
参考：https://www.jianshu.com/p/486b0965c296

---

## 一、基本介绍
### 1、对于一次IO访问，需要经历两个阶段：
- 准备数据。
- 将数据从内核缓冲区拷贝到进程地址空间

### 2、Linux 五种I/O模型
- 阻塞I/O
- 非阻塞I/O
- I/O复用(select 和 poll)
- 信号驱动I/O
- 异步I/O<br>

其中，前四个被称为同步IO。


## 二、五种IO模型
### 1、阻塞I/O

![image](E:/black user/Java/有道云截图/20150405_VKYH.png)<br>

当用户进程调用了 recvfrom() 时，内核进入 IO 的第一个阶段：准备数据（内核需要等待足够多的数据再拷贝），这个过程需要等待，用户进程会被阻塞，等内核将数据准备好，然后拷贝到用户地址空间，内核返回结果，用户进程才从阻塞态进入就绪态。

Linux 中，默认情况下所有的 Socket 都是阻塞的。
### 2、非阻塞 IO

![image](E:/black user/Java/有道云截图/191353249597648.jpg)<br>

当用户进程发出 recvfrom() 调用时，如果 Kernel 中的数据还没有准备好，那么它并不会阻塞用户进程，而是立刻返回一个 Error。用户进程判断结果是一个 Error 时，它就知道数据还没有准备好，于是它可以再次发送 recvfrom() 调用。一旦 Kernel 中的数据准备好了，并且又再次收到了用户进程的系统调用，那么它马上就将数据拷贝到了用户内存，然后返回

非阻塞 IO 模式下用户进程需要不断地询问内核的数据准备好了没有，如果没有准备好，那么在某些场景中，用户进程可以去做别的事情而不需要一直等待。

Linux 下可以通过设置 Socket 为 non-blocking 模式。

### 3、信号驱动 IO
![image](E:/black user/Java/有道云截图/191405186935031.jpg)<br>

内核文件描述符就绪后，通过 Signal（信号）通知用户进程，用户进程再通过系统调用读取数据。此方式属于同步 IO，因为实际读取数据到用户进程缓存的工作仍然是由用户进程自己负责的。

### 4、异步 IO
![image](E:/black user/Java/有道云截图/191409181463512.jpg)<br>

用户进程发起 read() 调用之后，立刻就可以开始去做其它的事。内核收到一个异步 IO read 之后，会立刻返回，不会阻塞用户进程。内核会等待数据准备完成，然后将数据拷贝到用户内存，当这一切都完成之后，内核会给用户进程发送一个 Signal（信号），告诉它 read() 完成了。用户进程再从用户内存读取数据。

### 5、IO 多路复用
通过一种机制，一个进程可以监视多个文件描述符fd（套接字描述符），一旦某个文件描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。这样就不需要每个用户进程不断的询问内核数据准备好了没有<br>

![image](E:/black user/Java/有道云截图/191359495371191.jpg)





