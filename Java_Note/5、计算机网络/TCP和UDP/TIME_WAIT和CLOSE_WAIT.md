# TIME_WAIT

TIME_WAIT 表示主动关闭，CLOSE_WAIT 表示被动关闭。

# 一、TIME_WAIT基本介绍

TCP 连接中，「**主动发起关闭连接**」的一端，会进入 time_wait 状态

## 1、产生原因

TIME_WAIT 会定时2MSL的回收资源，并不会占用很大资源的，除非短时间内接受大量请求或者受到攻击。

- 大量的短连接存在
- 特别是 HTTP 请求中，如果 connection 头部取值被设置为 close 时，基本都由「服务端」发起**主动关闭连接**
- 而，TCP 四次挥手关闭连接机制中，为了保证 ACK 重发和丢弃延迟数据，设置 **time_wait 为 2 倍的 MSL**（报文最大存活时间）



## 2、作用

### 2.1、可靠的终止TCP连接

若处于time_wait的client发送给server的确认报文段丢失的话，server将在此又一次发送FIN报文段，那么client必须处于一个可接收的状态，就是time_wait而不是close状态。

因为MSL是最大报文生存时间，如果在1个MSL时间内自己发送的ACK对方没有收到那就注定收不到了，而且对方肯定还会发送FIN，那么一个FIN发送过来的最长时间也是1个MSL，所以这里要等待2MSL。

### 2.2、 允许老的报文在网络中消逝（处理延迟到达的报文）

防止类似与“三次握手”中提到了的**“已经失效的连接请求报文段”出现在本连接**中。客户端发送完最后一个确认报文后，在这个**2MSL**时间中，足以保证使本次连接所产生的所有报文段都从网络中消失。这样新的连接中不会接受到旧连接的请求报文。



## 3、解决方法

### 3.1 服务端

优化服务器参数

- 允许 `time_wait` 状态的 socket 被**重用**
- 缩减 `time_wait` 时间，设置为 `1 MSL`（即，2 mins）

### 3.2 客户端

HTTP 请求的头部，connection 设置为 keep-alive，保持存活一段时间：现在的浏览器，一般都这么进行







# 二、常见问题

## 1、如果网络连接中出现大量 TIME_WAIT 状态所带来的危害?

time_wait 状态下，TCP 连接占用的端口，无法被再次使用。TCP 端口数量，上限是 6.5w（`65535`，16 bit）

大量 time_wait 状态存在，会导致新建 TCP 连接会出错，**address already in use : connect** 异常



如果系统中有很多 socket 处于 TIME_WAIT 状态,当需要创建新的 socket 连接的时 候可能会受到影响,这也会影响到系统的扩展性。
     之所以 TIME_WAIT 能够影响系统的扩展性是因为在一个 TCP 连接中,一个 Socket 如果 关闭的话,它将保持 TIME_WAIT 状态大约 1-4 分钟 。如果很多连接快速的打开和关闭的 话,系统中处于 TIME_WAIT 状态的 socket 将会积累很多,由于**本地端口数量的限制,**同一 时间只有有限数量的 socket 连接可以建立,如果太多的 socket 处于 TIME_WAIT 状态,你 会发现,由于用于新建连接的本地端口太缺乏,将会很难再建立新的对外连接。

## 2、 如何消除大量 TCP 短连接引发的 TIME_WAIT?

1、**可以改为长连接**,但代价较大,长连接太多会导致服务器性能问题,而且 PHP 等脚 本语言,需要通过 proxy 之类的软件才能实现长连接;

2、修改 ipv4.ip_local_port_range,增大可用端口范围,但只能缓解问题,不能根本 解决问题;

3、客户端程序中设置 socket 的 *SO_LINGER* 选项;

4、客户端机器打开 tcp_tw_recycle 和 tcp_timestamps 选项;

5、客户端机器打开 tcp_tw_reuse 和 tcp_timestamps 选项;

6、客户端机器设置 tcp_max_tw_buckets 为一个很小的值;



## 3、TIME_WAIT 的时间? 当关闭连接时最后一个 ACK 丢失怎么办?

### 3.1、TIME_WAIT 的时间?

 就是 2 个报文最长生存时间(2MSL)**,1 个 MSL 在 RFC 上建议是 2 分钟,而实现传统上 使用 30 秒**,因而,TIME_WAIT 状态一般维持在 1-4 分钟。

### 3.2、当关闭连接时最后一个 ACK 丢失怎么办?

 如果最后一个 ACK 丢失的话,TCP 就会认为它的 FIN 丢失,进行重发 FIN。 在客户端收到 FIN 后,就会设置一个 2MSL 计时器,2MSL 计时器可以使客户等 待足够长的时间,使得在 ACK 丢失的情况下,可以等到下一个 FIN 的到来。如 果在 TIME-WAIT 状态汇总有一个新的 FIN 到达了,客户就会发送一个新的 ACK, 并重新设置 2MSL 计时器。
     如果重传 FIN 到达客户端时,客户端已经进入 CLOSED 状态时,那么客户就 永远收不到这个重传的 FIN 报文段,服务器收不到 ACK,服务器无法关闭连接。



## 4、 time_wait 是「服务器端」的状态？or 「客户端」的状态？

- RE：time_wait 是「主动关闭 TCP 连接」一方的状态，可能是「客服端」的，也可能是「服务器端」的
- 一般情况下，都是「客户端」所处的状态；「服务器端」一般设置「不主动关闭连接」





# CLOSE_WAIT

TIME_WAIT状态可以通过优化服务器参数得到解决，因为发生TIME_WAIT的情况是服务器自己可控的，要么就是对方连接的异常，要么就是自己没有迅速回收资源，总之不是由于自己程序错误导致的。

但是CLOSE_WAIT就不一样了，从上面的图可以看出来，如果一直保持在CLOSE_WAIT状态，那么只有一种情况，就是在对方关闭连接之后服务器程序自己没有进一步发出ack信号。换句话说，就是在对方连接关闭之后，程序里没有检测到，或者程序压根就忘记了这个时候需要关闭连接，于是这个资源就一直被程序占着。

个人觉得这种情况，通过服务器内核参数也没办法解决，服务器对于程序抢占的资源没有主动回收的权利，除非终止程序运行。









## 参考文章

[服务器TIME_WAIT和CLOSE_WAIT详解和解决办法](https://www.cnblogs.com/sunxucool/p/3449068.html)

[面试官问：大量的 TIME_WAIT 状态 TCP 连接，对业务有什么影响？怎么处理？](https://mp.weixin.qq.com/s?__biz=Mzg2NzA4MTkxNQ==&mid=2247490191&idx=2&sn=1e4db106cf6e1d5ad3b25df3bc859562&chksm=ce40515bf937d84d0ca6d3a497812a71714fd5005cb53a59a8f673b19c2a88279e9cd944e98c&scene=126&sessionid=1600238568&key=644b115416bee241df5f681eee9008b587ec750f8c669915c174967b5febc7881d88b5650e21a935d7301f4343a9af2394e22130c57b8d2625b57343efa6ad2f5bce97f1e9762462960076e4f2556aed811d4c7263839c7cb0bf1361c02aff7ae64e1a93c04627747e121e2e270cfeb9c82bd5691f190b36704ba8a8441bfafd&ascene=1&uin=MTg5MjE1NTkyOQ%3D%3D&devicetype=Windows+10+x64&version=62090538&lang=zh_CN&exportkey=A5uFsvB8FMFFTSqjq5uAJko%3D&pass_ticket=5j8cd6Eb8sgqzfsqqd8rEDl56jIA7UUoOs2BGoPChlXHLwV492c4imtQjdQEcLtP&wx_header=0)







