# NIO  (New IO)：同步、非阻塞
--------------------------------------------------------------------------------
参考：https://www.cnblogs.com/sxkgeek/p/9488703.html<br>
https://blog.csdn.net/a953713428/article/details/64907250

# 一、基本介绍
## 1、IO与NIO区别
&emsp;&emsp;I/O 与 NIO 最重要的区别是数据打包和传输的方式，I/O 以流的方式处理数据，而 NIO 以块的方式处理数据，且NIO 是非阻塞的

&emsp;&emsp;面向流的 I/O 一次处理一个字节数据：一个输入流产生一个字节数据，一个输出流消费一个字节数据。为流式数据创建过滤器非常容易，链接几个过滤器，以便每个过滤器只负责复杂处理机制的一部分。不利的一面是，面向流的 I/O 通常相当慢。<br>
&emsp;&emsp;面向块的 I/O 一次处理一个数据块，按块处理数据比按流处理数据要快得多。但是面向块的 I/O 缺少一些面向流的 I/O 所具有的优雅性和简单性

## 2、主要组成部分
&emsp;&emsp;Channel（通道）、Buffer（缓冲区）、Selector（选择器）



# 二、组成部分

## 1. 通道（Channel ）
### 1）基本概念
&emsp;&emsp;Channel是一个对象，可以通过它读取和写入数据。可以把它看做是IO中的流，不同的是：<br>
- Channel是双向的，既可以读又可以写，而流是单向的
- Channel可以进行异步的读写
- 对Channel的读写必须通过buffer对象
- 
注意：所有数据都通过Buffer对象处理，所以，您永远不会将字节直接写入到Channel中；<br>
&emsp;&emsp;而是将数据从Channel读入Buffer，再从Buffer获取这个字节。


### 2）类型：
- FileChannel：从文件中读写数据；
- DatagramChannel：通过 UDP 读写网络中数据；
- SocketChannel：通过 TCP 读写网络中数据；
- ServerSocketChannel：可以监听新进来的 TCP 连接，对每一个新进来的连接都会创建一个 SocketChannel
 

## 2、缓冲区（buffer）

### 1）基本概念
&emsp;&emsp;数据从Channel读入Buffer，再从Buffer获取这个字节

### 2）状态变量
- capacity：最大容量；
- position：当前已经读写的字节数；
- limit：还可以读写的字节数

### 3）主要方法
- flip()         ：	方法将 Buffer 从写模式切换到读模式，在读模式下，可以读取之前写入到 Buffer 的所有数据
- clear() 	：	方法会清空整个缓冲区。
- compact() ：	方法只会清除已经读过的数据。<br>
任何未读的数据都被移到缓冲区的起始处，新写入的数据将放到缓冲区未读数据的后面。


### 4）状态变量的改变过程举例：
① 新建一个大小为 8 个字节的缓冲区，此时 position 为 0，而 limit = capacity = 8。capacity 变量不会改变，下面的讨论会忽略它。

② 从输入通道中读取 5 个字节数据写入缓冲区中，此时 position 为 5，limit 保持不变。

③ 在将缓冲区的数据写到输出通道之前，需要先调用 flip() 方法，这个方法将 limit 设置为当前 position，并将 position 设置为 0。

④ 从缓冲区中取 4 个字节到输出缓冲中，此时 position 设为 4。

⑤ 最后需要调用 clear() 方法来清空缓冲区，此时 position 和 limit 都被设置为最初位置。

### 5）文件 NIO 实例

```
public static void fastCopy(String src, String dist) throws IOException {

    /* 获得源文件的输入字节流 */
    FileInputStream fin = new FileInputStream(src);

    /* 获取输入字节流的文件通道 */
    FileChannel fcin = fin.getChannel();

    /* 获取目标文件的输出字节流 */
    FileOutputStream fout = new FileOutputStream(dist);

    /* 获取输出字节流的文件通道 */
    FileChannel fcout = fout.getChannel();

    /* 为缓冲区分配 1024 个字节 */
    ByteBuffer buffer = ByteBuffer.allocateDirect(1024);

    while (true) {

        /* 从输入通道中读取数据到缓冲区中 */
        int r = fcin.read(buffer);

        /* read() 返回 -1 表示 EOF */
        if (r == -1) {
            break;
        }

        /* 切换读写 */
        buffer.flip();

        /* 把缓冲区的内容写入输出文件中 */
        fcout.write(buffer);

        /* 清空缓冲区 */
        buffer.clear();
    }
}
```




## 3、Selector（选择器对象）
### 1）基本介绍
&emsp;&emsp;NIO 常常被叫做非阻塞 IO，NIO 实现了 IO 多路复用中的 Reactor 模型，一个线程 Thread 使用一个选择器 Selector 通过轮询的方式去监听多个通道 Channel 上的事件，从而让一个线程就可以处理多个事件（channel），就可以处理大量网络连接。

&emsp;&emsp;通过配置监听的通道 Channel 为非阻塞，那么当 Channel 上的 IO 事件还未到达时，就不会进入阻塞状态一直等待，而是继续轮询其它 Channel，找到 IO 事件已经到达的 Channel 执行。<br>
&emsp;&emsp;因为创建和切换线程的开销很大，因此使用一个线程来处理多个事件而不是一个线程处理一个事件，对于 IO 密集型的应用具有很好地性能。
注意：只有套接字 Channel 才能配置为非阻塞，而 FileChannel 不能，为 FileChannel 配置非阻塞也没有意义。

### 2）实现形式
#### i:创建一个Selector

```
Selector selector = Selector.open();
```

#### ii:注册Channel到Selector

```
//Channel.configureBlocking(false);
SocketChannel.configureBlocking(false);
SelectionKey key =channel.register(selector,SelectionKey.OP_READ);
```
### 3)注册事件
&emsp;&emsp;将通道注册到选择器上时，还需要指定要注册的具体事件，主要有以下几类：

```
SelectionKey.OP_CONNECT
SelectionKey.OP_ACCEPT
SelectionKey.OP_READ
SelectionKey.OP_WRITE
```


&emsp;&emsp;注意:注册的Channel 必须设置成==异步模式== 才可以,否则异步IO就无法工作，这就意味着我们不能把一个FileChannel注册到Selector，因为FileChannel没有异步模式，但是网络编程中的SocketChannel是可以的。

# 三、NIO多路复用
## 1、主要步骤和元素：
&emsp;&emsp;首先，通过 Selector.open() 创建一个 Selector，作为类似调度员的角色；<br>
&emsp;&emsp;然后，创建一个 ServerSocketChannel，并且向 Selector 注册，通过指定 SelectionKey.OP_ACCEPT，告诉调度员，它关注的是新的连接请求；

## 2、为什么我们要明确配置非阻塞模式呢？
&emsp;&emsp;这是因为阻塞模式下，注册操作是不允许的，会抛出IllegalBlockingModeException 异常；

&emsp;&emsp;Selector 阻塞在 select 操作，当有 Channel 发生接入请求，就会被唤醒；

&emsp;&emsp;IO 都是同步阻塞模式，所以需要多线程以实现多任务处理。而 NIO 则是利用了单线程轮询事件的机制，通过高效地定位就绪的 Channel，来决定做什么，仅仅 select 阶段是阻塞的，可以有效避免大量客户端连接时，频繁线程切换带来的问题，应用的扩展能力有了非常大的提高


# 四、内存映射
## 1、基本介绍
&emsp;&emsp;这个功能主要是为了提高大文件的读写速度而设计的。内存映射文件(memory-mappedfile)能让你创建和修改那些大到无法读入内存的文件。有了内存映射文件，你就可以认为文件已经全部读进了内存，然后把它当成一个非常大的数组来访问了。将文件的一段区域映射到内存中，比传统的文件处理速度要快很多。内存映射文件它虽然最终也是要从磁盘读取数据，但是它并不需要将数据读取到OS内核缓冲区，而是直接将进程的用户私有地址空间中的一部分区域与文件对象建立起映射关系，就好像直接从内存中读、写文件一样，速度当然快了。
## 2、优点：
&emsp;&emsp;用户进程将文件数据视为内存，因此不需要发出read()或write()系统调用。<br>
&emsp;&emsp;当用户进程触摸映射的内存空间时，将自动生成页面错误，以从磁盘引入文件数据。<br> &emsp;&emsp;如果用户修改映射的内存空间，受影响的页面将自动标记为脏，并随后刷新到磁盘以更新文件。<br>
&emsp;&emsp;操作系统的虚拟内存子系统将执行页面的智能缓存，根据系统负载自动管理内存。<br>
&emsp;&emsp;数据始终是页面对齐的，不需要缓冲区复制。<br>
&emsp;&emsp;可以映射非常大的文件，而不消耗大量内存来复制数据。

# 五、 字符及编码
## 1、字符编码方案：
&emsp;&emsp;编码方案定义了如何把字符编码的序列表达为字节序列。字符编码的数值不需要与编码字节相同，也不需要是一对一或一对多个的关系。原则上，把字符集编码和解码近似视为对象的序列化和反序列化。

## 2、
&emsp;&emsp;大部分的操作系统在I/O与文件存储方面仍是以字节为导向的，所以无论使用何种编码，Unicode或其他编码，在字节序列和字符集编码之间仍需要进行转化。


# 六、零拷贝
&emsp;&emsp;Java NIO中提供的FileChannel拥有transferTo和transferFrom两个方法，可直接把FileChannel中的数据拷贝到另外一个Channel，或者直接把另外一个Channel中的数据拷贝到FileChannel。该接口常被用于高效的网络/文件的数据传输和大文件拷贝。在操作系统支持的情况下，通过该方法传输数据并不需要将源数据从内核态拷贝到用户态，再从用户态拷贝到目标通道的内核态，同时也避免了两次用户态和内核态间的上下文切换，也即使用了“零拷贝”，所以其性能一般高于Java IO中提供的方法。

```
public class NIOClient {

  public static void main(String[] args) throws IOException, InterruptedException {
    SocketChannel socketChannel = SocketChannel.open();
    InetSocketAddress address = new InetSocketAddress(1234);
    socketChannel.connect(address);

    RandomAccessFile file = new RandomAccessFile(
        NIOClient.class.getClassLoader().getResource("test.txt").getFile(), "rw");
    FileChannel channel = file.getChannel();
    channel.transferTo(0, channel.size(), socketChannel);
    channel.close();
    file.close();
    socketChannel.close();
  }
}
```







# 六、学习总结
## 1、NIO的新特性
总的来说java 中的IO 和NIO的区别主要有3点：
- IO是面向流的，NIO是面向缓冲的；
- IO是阻塞的，NIO是非阻塞的；
- IO是单线程的，NIO 是通过选择器来模拟多线程的

## 2、新特点
- 内存映射技术    
- 字符及编码    
- 非阻塞I/O       
- 文件锁定









