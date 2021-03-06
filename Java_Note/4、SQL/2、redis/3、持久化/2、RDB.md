

# RDB（Redis DataBase）  

# 一、基本介绍

## 1、什么是RDB  

在主从复制中，rdb就是备用了！从机上面！  

在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的**Snapshot快照**，它恢复时是将快照文件直接读到内存里。

## 2、原理

fork和cow。fork是指redis通过创建子进程来进行RDB操作，cow指的是**copy on write**，子进程创建后，父子进程共享数据段，父进程继续提供读写服务，写脏的页面数据会逐渐和子进程分离开来。

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200924220727.png" style="zoom:67%;" />



Redis会单独创建**（fork）**一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的。

这就确保了极高的性能。如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效。RDB的缺点是最后一次持久化后的数据可能丢失。我们默认的就是RDB，一般情况下不需要修改这个配置！



## RDB文件

有时候在生产环境我们会将这个文件进行备份  

rdb保存的文件是**dump.rdb** 都是在我们的配置文件中快照中进行配置的  

![image-20200924220911909](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200924220911.png)



## 触发机制  

- save的规则满足的情况下，会自动触发rdb规则

- 执行 flushall 命令，也会触发我们的rdb规则！

- 退出redis，也会产生 rdb 文件！

- 备份就自动生成一个 dump.rdb  




## 如何恢复rdb文件

1、只需要将rdb文件放在我们redis启动目录就可以，redis启动的时候会自动检查dump.rdb 恢复其中的数据！

2、查看需要存在的位置

```
127.0.0.1:6379> config get dir
1) "dir"
2) "/usr/local/redis/bin"
```

几乎就他自己默认的配置就够用了，但是我们还是需要去学习  





## 优点：

1、适合大规模的数据恢复！

2、对数据的完整性要不高！

## 缺点：

1、需要一定的时间间隔进程操作！如果redis意外宕机了，这个最后一次修改数据就没有的了！

2、fork进程的时候，会占用一定的内容空间  











