# redis

## 官网：

​			[redis](https://redis.io/ )       	[redis中文官网](http://www.redis.cn/)	 	[redis中文网](https://www.redis.com.cn/redis-tutorial)			[redis安装](https://github.com/microsoftarchive/redis/releases/tag/win-3.2.100)



# 一、基本介绍

## 1、简介

Redis:REmote DIctionary Server(远程字典服务)

是完全开源免费的，用**C语言**编写的，遵守BSD协议，高性能的(**key/value**)**分布式内存数据库**，基于内存运行并支持**持久化**的**NoSQL**数据库。

redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件，并且在此基础上实现了master-slave(主从)同步

Redis 是一个开源（BSD许可）的，内存中的数据结构存储系统，它可以用作数据库、缓存和消息中间件MQ。

 它支持多种类型的数据结构，如 字符串（strings）， 散列（hashes）， 列表（lists）， 集合（sets）， 有序集合（sorted sets） 与范围查询， bitmaps， hyperloglogs 和 地理空间（geospatial） 索引半径查询。 

Redis 内置了 复制（replication），LUA脚本（Lua scripting）， LRU驱动事件（LRU eviction），事务（transactions） 和不同级别的 磁盘持久化（persistence）， 并通过Redis哨兵（Sentinel）和自动 分区（Cluster）提供高可用性（high availability）



## 2、redis主要文件

### 1）linux

|      文件名      |       说明        |
| :--------------: | :---------------: |
|   redis-server   |    redis服务器    |
|    redis-cli     | redis命令行客户端 |
| redis-benchmark  | redis性能测试工具 |
| redis-check-aof  |  aof文件修复工具  |
| redis-check-dump |  RDB文件检查工具  |

### 2）windows

​	是以上文件名+.exe；注意：

​	redis.windows-service.conf：*将redis作为系统服务的配置*

​	redis.windows.conf:					将redis作为普通软件使用的配置，命令行关闭则redis关闭



## 3、Redis 能干嘛？

内存存储、持久化，内存中是断电即失、所以说持久化很重要（rdb、aof）

效率高，可以用于高速缓存

发布订阅系统

地图信息分析

计时器、计数器（浏览量！）

........



## 4、Redis有哪些优缺点

### 优点

- 读写性能优异， Redis能读的速度是110000次/s，写的速度是81000次/s。
- 支持数据持久化，支持AOF和RDB两种持久化方式。
- 支持事务，Redis的所有操作都是原子性的，同时Redis还支持对几个操作合并后的原子性执行。
- 数据结构丰富，除了支持string类型的value外还支持hash、set、zset、list等数据结构。
- 支持主从复制，主机会自动将数据同步到从机，可以进行读写分离。

### 缺点

- 数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上。
- Redis 不具备自动容错和恢复功能，主机从机的宕机都会导致前端部分读写请求失败，需要等待机器重启或者手动切换前端的IP才能恢复。
- 主机宕机，宕机前有部分数据未能及时同步到从机，切换IP后还会引入数据不一致的问题，降低了系统的可用性。
- Redis 较难支持在线扩容，在集群容量达到上限时在线扩容会变得很复杂。为避免这一问题，运维人员在系统上线时必须确保有足够的空间，这对资源造成了很大的浪费。







# 二、基础知识

## 1、Redis 是单线程的?

明白Redis是很快的，官方表示，Redis是基于**内存操作**，**CPU不是Redis性能瓶颈**，Redis的瓶颈是根据机器的**内存和网络带宽**，既然可以使用单线程来实现，就使用单线程了！所有就使用了单线程了！

Redis 确实是单线程模型，指的是执行 Redis 命令的核心模块是单线程的，而不是整个 Redis 实例就一个线程，Redis 其他模块还有各自模块的线程的。

Redis基于**Reactor模式**开发了网络事件处理器，这个处理器被称为文件事件处理器。它的组成结构为4部分：多个套接字、IO多路复用程序、文件事件分派器、事件处理器。

因为**文件事件分派器队列的消费是单线程**的，所以Redis才叫单线程模型。

**Redis6 版本中引入了多线程。**



### 1.1 Redis 为什么单线程还这么快？

Redis 利用了**多路 I/O 复用机制**，处理客户端请求时，不会阻塞主线程；

Redis 单纯执行（大多数指令）一个指令不到 **1 微秒**，如此，单核 CPU 一秒就能处理 1 百万个指令（大概对应着几十万个请求吧），用不着实现多线程（**网络才是瓶颈**）。



先去CPU > 内存 > 硬盘的速度要有所了解！

核心：redis 是将所有的数据全部放在**内存**中的，所以说使用单线程去操作效率就是最高的，多线程（CPU上下文会切换：耗时的操作！！！），对于内存系统来说，如果没有上下文切换效率就是最高的！多次读写都是在一个CPU上的，在内存情况下，这个就是最佳的方案 ！  





## 2、redis数据库

redis默认有16个数据库  

```bash
127.0.0.1:6379> select 3 # 切换数据库
OK
127.0.0.1:6379[3]> DBSIZE # 查看当前DB大小！
(integer) 0
```



## 3、性能测试工具

```
redis-benchmark
```



## 4、Redis为什么这么快

1、完全**基于内存**，绝大部分请求是纯粹的内存操作，非常快速。数据存在内存中，类似于 HashMap，HashMap 的优势就是查找和操作的时间复杂度都是O(1)；

2、**数据结构简单**，对数据操作也简单，Redis 中的数据结构是专门进行设计的；

3、采用**单线程**，避免了不必要的上下文切换和竞争条件，也不存在多进程或者多线程导致的切换而消耗 CPU，不用去考虑各种锁的问题，不存在加锁释放锁操作，没有因为可能出现死锁而导致的性能消耗；

4、使用**多路 I/O 复用模型**，非阻塞 IO；

5、使用底层模型不同，它们之间底层实现方式以及与客户端之间通信的应用协议不一样，Redis 直接自己构建了 VM 机制 ，因为一般的系统调用系统函数的话，会浪费一定的时间去移动和请求；







# 三、学习脑图

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200929212258.webp" alt="640 (E:\black user\Java\有道云截图\640-1590579338465.webp)" style="zoom:200%;" />













