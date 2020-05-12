# IO多路复用

---
# 一、基本介绍
## 1、基本概念
&emsp;&emsp;I/O就是指的我们网络I/O,多路指多个TCP连接(或多个Channel)，复用指复用一个或少量线程。串起来理解就是==很多个网络I/O复用一个或少量的线程来处理这些连接==。

![image](https://www.pdai.tech/_images/io/java-io-nio-1.png)

## 2、四种实现方式
&emsp;&emsp;select、poll、epoll、kqueue

![image.png](WEBRESOURCE1cd592ef54c2ace7029e11e27a9e4677)
## 3、使用场景
&emsp;&emsp;多路复用IO技术最适用的是“高并发”场景，所谓==高并发是指1毫秒内至少同时有上千个连接请求准备好==。其他情况下多路复用IO技术发挥不出来它的优势。
## 4、特征
&emsp;&emsp;多路复用IO技术是需要==操作系统==进行支持的，是操作系统的内核实现。<br>
&emsp;&emsp;其特点就是操作系统可以同时扫描同一个端口上不同网络连接的时间。


# 二、重要概念
## 1、Channel
&emsp;&emsp;通道，被建立的一个应用程序和操作系统交互事件、传递内容的渠道(注意是连接到操作系统)。<br>
&emsp;&emsp;一个通道会有一个专属的文件状态描述符。
## 2、Buffer（数据缓存区）
### 1）基本介绍
&emsp;&emsp; 在JAVA NIO 框架中，为了保证每个通道的数据读写速度JAVA NIO 框架为每一种需要支持数据读写的通道集成了Buffer的支持。
### 2）两种工作模式
&emsp;&emsp;写模式和读模式。<br>
&emsp;&emsp;在读模式下，应用程序只能从Buffer中读取数据，不能进行写操作。<br>
&emsp;&emsp;但是在写模式下，应用程序是可以进行读操作的，这就表示可能会出现脏读的情况。所以一旦您决定要从Buffer中读取数据，一定要将Buffer的状态改为读模式。<br>

![image](https://www.pdai.tech/_images/io/java-io-nio-3.png)<br>

- position: 缓存区目前这在操作的数据块位置 
- limit: 缓存区最大可以进行操作的位置。缓存区的读写状态正式由这个属性控制的
- capacity: 缓存区的最大容量。这个容量是在缓存区创建时进行指定的。由于高并发时通道数量往往会很庞大，所以每一个缓存区的容量最好不要过大。


## 3、Selector（选择器）