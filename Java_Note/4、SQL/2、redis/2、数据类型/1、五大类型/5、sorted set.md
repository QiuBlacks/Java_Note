# sorted set：有序集合

# 一、基础知识

## 1、基本介绍

它类似于 Java 中的 **SortedSet** 和 **HashMap** 的结合体，一方面它是一个 set 保证了内部 value 的唯一性，另一方面又可以给每个 value 赋予一个排序的权重值 score，来达到 **排序** 的目的。

它的内部实现就依赖了一种叫做 **「跳跃链表」** 的数据结构。

[](https://mp.weixin.qq.com/s?__biz=MzAwNDA2OTM1Ng==&mid=2453141687&idx=2&sn=23936a54d263d56cf26972a00a287feb&scene=21#wechat_redirect)



2、

支持快速插入和删除

有序且支持范围查询



# 二、基本命令

## 1、增

### 1.1 zadd

在set的基础上， zset key **score** value

```bash
127.0.0.1:6379> zadd myset 1 one # 添加一个值
(integer) 1
127.0.0.1:6379> zadd myset 2 two 3 three # 添加多个值
(integer) 2
127.0.0.1:6379> ZRANGE myset 0 -1
1) "one"
2) "two"
3) "three"
```



## 2、删

### 2.1 zrem

```
# 移除rem中的元素
127.0.0.1:6379> zrange salary 0 -1
1) "kaungshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> zrem salary xiaohong # 移除有序集合中的指定元素
(integer) 1
127.0.0.1:6379> zrange salary 0 -1
1) "kaungshen"
2) "zhangsan"
127.0.0.1:6379> zcard salary # 获取有序集合中的个数
(integer) 2
```



## 3、查

### 3.1 ZRANGE  

查询元素



### 3.2 ZRANGEBYSCORE  

ZRANGEBYSCORE  setname min max   withscores  

根据score**从小到大**进行范围排序

```bash
127.0.0.1:6379> zadd salary 2500 xiaohong # 添加三个用户
(integer) 1
127.0.0.1:6379> zadd salary 5000 zhangsan
(integer) 1
127.0.0.1:6379> zadd salary 500 kaungshen
(integer) 1
# ZRANGEBYSCORE key min max
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf # 显示全部的用户 从小到大！
1) "kaungshen"
2) "xiaohong"
3) "zhangsan"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf +inf withscores # 显示全部的用户并且附带成
绩 1)
"kaungshen"
2) "500"
3) "xiaohong"
4) "2500"
5) "zhangsan"
6) "5000"
127.0.0.1:6379> ZRANGEBYSCORE salary -inf 2500 withscores # 显示工资小于2500员工的升
序排序！
1) "kaungshen"
2) "500"
3) "xiaohong"
4) "2500"
```

### 3.3 ZREVRANGE  

根据score**从大到小**进行范围排序

```
127.0.0.1:6379> ZREVRANGE salary 0 -1 # 从大到进行排序！
1) "zhangsan"
2) "kaungshen"
```



### 3.4 zcount

```
127.0.0.1:6379> zadd myset 1 hello
(integer) 1
127.0.0.1:6379> zadd myset 2 world 3 kuangshen
(integer) 2
127.0.0.1:6379> zcount myset 1 3 # 获取指定区间的成员数量！
(integer) 3
127.0.0.1:6379> zcount myset 1 2
(integer) 2
```



## 4、改









# 三、跳表：Skip List

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201104195813.jpeg" alt="img" style="zoom:67%;" />

链表 + 索引

## 1、为什么使用跳跃表

首先，因为 zset 要支持随机的插入和删除，所以它 **不宜使用数组来实现**，关于排序问题，我们也很容易就想到 **红黑树/ 平衡树** 这样的树形结构，为什么 Redis 不使用这样一些结构呢？

**性能考虑：** 在高并发的情况下，树形结构需要执行一些类似于 rebalance 这样的可能涉及整棵树的操作，相对来说跳跃表的变化只涉及局部 *(下面详细说)*；

**实现考虑：** 在复杂度与红黑树相同的情况下，跳跃表实现起来更简单，看起来也更加直观；





## 2、基础知识

### 2.1 查询的时间复杂度

第一级有N/2个节点，第二级有N/4个节点。。。。那么第**k**级索引有**N/(2^k)**个节点。

高度就是log2N，时间复杂度是O（logn）



### 2.2 索引动态更新

如果我们按照上面生成链表的方式，上面每一层链表的节点个数，是下面一层的节点个数的一半，这样查找过程就非常类似于一个二分查找，使得查找的时间复杂度可以降低到 *O(logn)*。

但是，这种方法在插入数据的时候有很大的问题。新插入一个节点之后，就会打乱上下相邻两层链表上节点个数严格的 2:1 的对应关系。如果要维持这种对应关系，就必须**把新插入的节点后面的所有节点 *（也包括新插入的节点）* 重新进行调整**，这会让时间复杂度重新蜕化成 *O(n)*。删除数据也有同样的问题。

**skiplist** 为了避免这一问题，它不要求上下相邻两层链表之间的节点个数有严格的对应关系，而是 **为每个节点随机出一个层数(level)**。比如，一个节点随机出的层数是 3，那么就把它链入到第 1 层到第 3 层这三层链表中。



## 3、底层实现















# 参考文章

[Redis-跳跃表](https://mp.weixin.qq.com/s?__biz=MzAwNDA2OTM1Ng==&mid=2453141687&idx=2&sn=23936a54d263d56cf26972a00a287feb&scene=21#wechat_redirect)

[Redis 跳表](https://yq.aliyun.com/articles/701175)











