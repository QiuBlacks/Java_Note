# 分布式session

# 一、问题引出

##  分布式session一致性问题

主要是seesion共享问题。比如集群中存在A、B两台服务器，用户在第一次访问网站时，Nginx通过其负载均衡机制将用户请求转发到A服务器，这时A服务器就会给用户创建一个Session。当用户第二次发送请求时，Nginx将其负载均衡到B服务器，而这时候B服务器并不存在Session，所以就会将用户踢到登录页面。这将大大降低用户体验度，导致用户的流失，这种情况是项目绝不应该出现的。



# 二、解决方案

## 1、客户端存储

直接将信息存储在cookie中

**缺点**：

- 数据存储在客户端，存在安全隐患
- cookie存储大小、类型存在限制
- 数据存储在cookie中，如果一次请求cookie过大，会给网络增加更大的开销



## 2、session绑定

原理：粘性Session是指将用户锁定到某一个服务器上，比如上面说的例子，用户第一次请求时，负载均衡器将用户的请求转发到了A服务器上，如果负载均衡器设置了粘性Session的话，那么**用户以后的每次请求都会转发到A服务器**上，相当于把用户和A服务器粘到了一块，这就是粘性Session机制。

优点：简单，不需要对session做任何处理。

缺点：缺乏容错性，如果当前访问的服务器发生故障，用户被转移到第二个服务器上时，他的session信息都将失效。

适用场景：发生故障对客户产生的影响较小；服务器发生故障是低概率事件。

实现方式：Nginx中的ip绑定策略，同一个ip只能在指定的同一个机器访问

以Nginx为例，在nginx安装目录下的conf目录中的nginx.conf文件

```
upstream mycluster{
    #这里添加的是上面启动好的两台Tomcat服务器
    ip_hash;#粘性Session
     server 192.168.22.229:8080 weight=1;
     server 192.168.22.230:8080 weight=1;
}
```



## 3、session同步（服务器:tomcat）

原理：任何一个服务器上的session发生改变（增删改），该节点会把这个 session的所有内容序列化，然后广播给所有其它节点，不管其他服务器需不需要session，以此来保证Session同步。

优点：可容错，各个服务器间session能够实时响应。

缺点：会对网络负荷造成一定压力，如果session量大的话可能会造成网络堵塞，拖慢服务器性能。

实现方式：

① 设置tomcat ，server.xml 开启tomcat集群功能 <distributable/>



## 4、session共享（常用）

使用分布式缓存方案比如memcached、Redis，但是要求Memcached或Redis必须是集群。

我们使用**spring-session**以及集成好的解决方案，存放在redis中

具体案例参考：https://www.cnblogs.com/SimpleWu/p/10118674.html

**实现原理：**

就是当Web服务器接收到http请求后，当请求进入对应的Filter进行过滤，将原本需要由web服务器创建会话的过程转交给Spring-Session进行创建，本来创建的会话保存在Web服务器内存中，通过Spring-Session创建的会话信息可以保存第三方的服务中，如：redis,mysql等。Web服务器之间通过连接第三方服务来共享数据，实现Session共享！

**优点**：

- 这是企业中使用的最多的一种方式
- spring为我们封装好了spring-session，直接引入依赖即可
- 数据保存在redis中，无缝接入，不存在任何安全隐患
- redis自身可做集群，搭建主从，同时方便管理

**缺点**：

- 多了一次网络调用，web容器需要向redis访问







# 参考文章

[4种分布式session解决方案](https://blog.csdn.net/qq_35620501/article/details/95047642)

[分布式session的几种实现方式](https://www.cnblogs.com/daofaziran/p/10933221.html)











