# List：列表

# 一、基础知识

## 1、基本介绍

在redis里面，我们可以把list玩成 栈（ Lpush Lpop）、队列、阻塞队列、消息队列 （Lpush Rpop）

所有的list命令都是用l开头的，Redis不区分大小命令  

![image-20200919162936972](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200919162957.png)



## 2、总结

- 他实际上是一个链表，before Node after ， left，right 都可以插入值
- 如果key 不存在，创建新的链表
- 如果key存在，新增内容
- 如果移除了所有值，空链表，也代表不存在！
- 在两边插入或者改动值，效率最高！ 中间元素，相对来说效率会低一点~
  



## 3、常用命令

- lpush key value：将一个值或者多个值，插入到列表的头部（左）
- lrange key 0 -1：获取key中所有的值
- rpush key value：将一个值或者多个值，插入到列表的尾部（右）
- lpop key：移除key的第一个元素
- rpop key：移除key的最后一个元素
- lindex key index：通过下标获得key中的某一个值
- llen list：返回列表的长度
- lrem key count value：移除key集合中指定count的value值
- ltrim key start stop：通过下标截取指定的长度，并改变key中的值
- rpoplpush source destination：移除列表的最后一个元素，将他添加到新的列表中
- lset key index value：如果存在index下标的值则更新为value，不存在则报错
- exists key：判断这个列表是否存在
- linsert key before|after pivot value：将某个具体的value值插入到列表中某个元素的前面或者后面





# 二、基本命令

## 1、插入

### 1.1 push

LPUSH：在队列左边插入

Rpush：在队列右边插入

LRANGE  : 输出是按从左到右的顺序

```bash
127.0.0.1:6379> LPUSH list one # 将一个值或者多个值，插入到列表头部 （左）
(integer) 1
127.0.0.1:6379> LPUSH list two
(integer) 2
127.0.0.1:6379> LPUSH list three
(integer) 3
127.0.0.1:6379> LRANGE list 0 -1 # 获取list中值！
1) "three"
2) "two"
3) "one"
127.0.0.1:6379> LRANGE list 0 1 # 通过区间获取具体的值！
1) "three"
2) "two"
127.0.0.1:6379> Rpush list righr # 将一个值或者多个值，插入到列表位部 （右）
(integer) 4
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "righr"
```

### 1.2 linsert  

将某个具体的value插入到列把你中某个元素的前面或者后面  

```
127.0.0.1:6379> Rpush mylist "hello"
(integer) 1
127.0.0.1:6379> Rpush mylist "world"
(integer) 2
127.0.0.1:6379> LINSERT mylist before "world" "other"
(integer) 3
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
127.0.0.1:6379> LINSERT mylist after world new
(integer) 4
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello"
2) "other"
3) "world"
4) "new
```





## 2、移除

### 2.1  pop

Lpop：从队列左边移除

Rpop：从队列右边移除

```
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
3) "one"
4) "righr"
127.0.0.1:6379> Lpop list # 移除list的左边第一个元素
"three"
127.0.0.1:6379> Rpop list # 移除list的右边最后一个元素
"righr"
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
```

### 2.2 Lrem  

Lrem  : 移除集合中指定个数的value

lrem key count element

```
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
4) "one"
127.0.0.1:6379> lrem list 1 one # 移除list集合中指定个数的value，精确匹配
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "three"
3) "two"
127.0.0.1:6379> lrem list 1 three
(integer) 1
127.0.0.1:6379> LRANGE list 0 -1
1) "three"
2) "two"
127.0.0.1:6379> Lpush list three
(integer) 3
127.0.0.1:6379> lrem list 2 three
(integer) 2
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
```



### 2.3 rpoplpush  ：

移除列表的右边最后一个元素，将他移动到新的列表中！ 

```
127.0.0.1:6379> rpush mylist "hello"
(integer) 1
127.0.0.1:6379> rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> rpoplpush mylist myotherlist # 移除列表的最后一个元素，将他移动到新的
列表中！
"hello2"
127.0.0.1:6379> lrange mylist 0 -1 # 查看原来的列表
1) "hello"
2) "hello1"
127.0.0.1:6379> lrange myotherlist 0 -1 # 查看目标列表中，确实存在改值！
1) "hello2
```

###  



## 3、查询

### 3.1 Lindex： 查询指定位置

```
127.0.0.1:6379> LRANGE list 0 -1
1) "two"
2) "one"
127.0.0.1:6379> lindex list 1 # 通过下标获得 list 中的某一个值！
"one"
127.0.0.1:6379> lindex list 0
"two"
```

### 3.2 Llen:查询长度

```
127.0.0.1:6379> Lpush list one
(integer) 1
127.0.0.1:6379> Lpush list two
(integer) 2
127.0.0.1:6379> Lpush list three
(integer) 3
127.0.0.1:6379> Llen list # 返回列表的长度
(integer) 3
```



## 4、更新

### 4.1 trim  

通过下标截取集合指定的长度

```
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> Rpush mylist "hello"
(integer) 1
127.0.0.1:6379> Rpush mylist "hello1"
(integer) 2
127.0.0.1:6379> Rpush mylist "hello2"
(integer) 3
127.0.0.1:6379> Rpush mylist "hello3"
(integer) 4
127.0.0.1:6379> ltrim mylist 1 2 # 通过下标截取指定的长度，这个list已经被改变了，截断了
只剩下截取的元素！
OK
127.0.0.1:6379> LRANGE mylist 0 -1
1) "hello1"
2) "hello2"
```



### 4.2 lset

将列表中指定下标的值替换为另外一个值

```
127.0.0.1:6379> EXISTS list # 判断这个列表是否存在
(integer) 0
127.0.0.1:6379> lset list 0 item # 如果不存在列表我们去更新就会报错
(error) ERR no such key
127.0.0.1:6379> lpush list value1
(integer) 1
127.0.0.1:6379> LRANGE list 0 0
1) "value1"
127.0.0.1:6379> lset list 0 item # 如果存在，更新当前下标的值
OK
127.0.0.1:6379> LRANGE list 0 0
1) "item"
127.0.0.1:6379> lset list 1 other # 如果不存在，则会报错！
(error) ERR index out of range
```

