# AOF（Append Only File）  

# 一、基础知识

## 1、基本介绍

将我们的所有**写操作**命令都记录下来，history，恢复的时候就把这个文件全部在执行一遍！  

以**日志的形式**来记录每个写操作，将Redis执行过的所有指令记录下来（**读操作不记录**），只许追加文件但不可以改写文件，**redis启动之初会读取appendonly.aof文件重新构建数据**，换言之，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作  

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200924234757.png" alt="image-20200924234757114" style="zoom:67%;" />



## 2、优缺点  

### 优点：

1、每一次修改都同步，文件的完整会更加好！

2、每秒同步一次，可能会丢失一秒的数据

3、从不同步，效率最高的！

### 缺点：

1、相对于数据文件来说，aof 远远大于 rdb，修复的速度也比 rdb慢！

2、Aof 运行效率也要比 rdb 慢，所以我们redis默认的配置就是rdb持久化  



# 二、AOF文件操作

## 1、文件位置

Aof保存的是 **redis/bin/appendonly.aof 文件**  

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200924234851.png" alt="image-20200924234850994" style="zoom:67%;" />

默认是不开启的，我们需要手动进行配置！我们只需要将 appendonly **改为ye**s就开启了 aof！重启，redis 就可以生效了！





## 2、AOF文件报错

如果这个 aof 文件有错误，这时候 redis 是启动不起来的吗，我们需要修复这个aof文件redis 给我们提供了一个工具 redis-check-aof --fix  

```bash
redis-check-aof --fix   appendonly.aof 
```



## 3、重写规则说明  

aof 默认就是文件的无限追加，文件会越来越大  

如果 aof 文件大于 64m，太大了！ fork一个新的进程来将我们的文件进行重写  

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200924235012.png" alt="image-20200924234946505" style="zoom:67%;" />









# 三、扩展

1、RDB 持久化方式能够在指定的时间间隔内对你的数据进行快照存储

2、AOF 持久化方式记录每次对服务器写的操作，当服务器重启的时候会重新执行这些命令来恢复原始
的数据，AOF命令以Redis 协议追加保存每次写的操作到文件末尾，Redis还能对AOF文件进行后台重
写，使得AOF文件的体积不至于过大。

3、**只做缓存，如果你只希望你的数据在服务器运行的时候存在，你也可以不使用任何持久化**

4、同时开启两种持久化方式

在这种情况下，当redis重启的时候**会优先载入AOF文件**来恢复原始的数据，因为在通常情况下AOF
文件保存的数据集要比RDB文件保存的数据集要完整。

RDB 的数据不实时，同时使用两者时服务器重启也只会找AOF文件，那要不要只使用AOF呢？

作者建议不要，因为RDB更适合用于备份数据库（AOF在不断变化不好备份），快速重启，而且不会有
AOF可能潜在的Bug，留着作为一个万一的手段。

## 5、性能建议

因为RDB文件只用作后备用途，建议只在Slave上持久化RDB文件，而且只要15分钟备份一次就够
了，只保留 save 900 1 这条规则。

如果Enable AOF ，好处是在最恶劣情况下也只会丢失不超过两秒数据，启动脚本较简单只load自
己的AOF文件就可以了，代价一是带来了持续的IO，二是AOF rewrite 的最后将 rewrite 过程中产
生的新数据写到新文件造成的阻塞几乎是不可避免的。只要硬盘许可，应该尽量减少AOF rewrite
的频率，AOF重写的基础大小默认值64M太小了，可以设到5G以上，默认超过原大小100%大小重
写可以改到适当的数值。

如果不Enable AOF ，仅靠 Master-Slave Repllcation 实现高可用性也可以，能省掉一大笔IO，也
减少了rewrite时带来的系统波动。代价是如果Master/Slave 同时倒掉，会丢失十几分钟的数据，
启动脚本也要比较两个 Master/Slave 中的 RDB文件，载入较新的那个，微博就是这种架构  

