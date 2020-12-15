# redis客户端

# 一、基本介绍

## 1、概念：

### 1）Jedis

是Redis的Java实现客户端，提供了**比较全面的Redis命令的支持**

### 2）Redisson

实现了分布式和可扩展的Java数据结构，提供很多分布式相关操作服务，例如，分布式锁，分布式集合，可通过Redis支持延迟队列。

和Jedis相比，功能较为简单，不支持字符串操作，不支持排序、事务、管道、分区等Redis特性。Redisson的宗旨是促进使用者对Redis的关注分离，从而让使用者能够将精力更集中地放在处理业务逻辑上。

### 3）Lettuce

高级Redis客户端，用于线程安全同步，异步和响应使用，支持集群，Sentinel，管道和编码器。

目前springboot默认使用的客户端。



## 2、优点

Jedis：比较全面的提供了Redis的操作特性

Redisson：促使使用者对Redis的关注分离，提供很多分布式相关操作服务，例如，分布式锁，分布式集合，可通过Redis支持延迟队列

Lettuce：主要在一些分布式缓存框架上使用比较多



## 3、可伸缩

Jedis：使用阻塞的I/O，且其方法调用都是同步的，程序流需要等到sockets处理完I/O才能执行，不支持异步。Jedis客户端实例不是线程安全的，所以需要通过连接池来使用Jedis。

Redisson：基于Netty框架的事件驱动的通信层，其方法调用是异步的。Redisson的API是线程安全的，所以可以操作单个Redisson连接来完成各种操作

Lettuce：基于Netty框架的事件驱动的通信层，其方法调用是异步的。Lettuce的API是线程安全的，所以可以操作单个Lettuce连接来完成各种操作











# 二、客户端安装

## 一、redis的py客户端

### 1.1、安装redis-py

```bash
wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
mkdir -p /usr/local/python3
cd /usr/local/python3
tar -xvf Python-3.6.3.tgz 
cd Python-3.6.3/
 ./configure --prefix=/usr/local/python3Dir
 make
 make install
cd /usr/bin
mv python python.bak
ln -s /usr/local/python3/bin/python3 /usr/bin/python

vi /usr/bin/yum
把文件开头第一行的 #!/usr/bin/python  改成  #!/usr/bin/python2.7
sudo vim /etc/profile
末尾添加：
export PATH=$PATH:/usr/local/python3/bin
```



```
pip3 install redis
```

参考：

​		[升级py](https://blog.csdn.net/weixin_41798704/article/details/88238222)

​		https://www.cnblogs.com/Jimc/p/10218387.html





## 2、redis的C客户端

### 2.1、安装hiredis

```java
wget https://github.com/redis/hiredis/archive/v0.14.0.tar.gz #获取压缩包
cd hiredis-0.14.0/
make -j
make install
cp libhiredis.so  /usr/lib 
/sbin/ldconfig
```

### 2.2、编译

```bash
 gcc -o  test  test.c  -l  hiredis
   ./text
```

