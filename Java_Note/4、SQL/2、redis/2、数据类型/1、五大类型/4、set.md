# 4、set（集合）



不可重复的









# 二、基本命令

## 1、插入

### 1.1 sadd

```bash
127.0.0.1:6379> sadd myset "hello" # set集合中添加匀速
(integer) 1
127.0.0.1:6379> sadd myset "kuangshen"
(integer) 1
127.0.0.1:6379> sadd myset "lovekuangshen"
(integer) 1
127.0.0.1:6379> SMEMBERS myset # 查看指定set的所有值
1) "hello"
2) "lovekuangshen"
3) "kuangshen"
127.0.0.1:6379> SISMEMBER myset hello # 判断某一个值是不是在set集合中！
(integer) 1
127.0.0.1:6379> SISMEMBER myset world
(integer) 0
```



## 2、查询

### 2.1 SMEMBERS

查看指定set的所有成员

SMEMBERS setname 

### 2.2 SISMEMBER

SISMEMBER setname value

判断某一个值是不是在set集合中



### 2.3 scard

获取set集合中的元素个数

```
127.0.0.1:6379> scard myset # 获取set集合中的内容元素个数
(integer) 4  
```



### 2.4 SRANDMEMBER

SRANDMEMBER  setname  nums

随机抽选出指定集合的N个元素

```bash
127.0.0.1:6379> SMEMBERS myset
1) "lovekuangshen2"
2) "lovekuangshen"
3) "kuangshen"
127.0.0.1:6379> SRANDMEMBER myset # 随机抽选出一个元素
"kuangshen"
127.0.0.1:6379> SRANDMEMBER myset
"kuangshen"
127.0.0.1:6379> SRANDMEMBER myset 2 # 随机抽选出指定个数的元素
1) "lovekuangshen"
2) "lovekuangshen2"
127.0.0.1:6379> SRANDMEMBER myset 2
1) "lovekuangshen"
2) "lovekuangshen2
```



## 3、删

### 3.1 srem

移除set集合中的指定元素

```
127.0.0.1:6379> srem myset hello # 移除set集合中的指定元素
(integer) 1
127.0.0.1:6379> scard myset
(integer) 3
127.0.0.1:6379> SMEMBERS myset
1) "lovekuangshen2"
2) "lovekuangshen"
3) "kuangshen"
```



### 3.2 spop

随机删除一个元素

```
127.0.0.1:6379> SMEMBERS myset
1) "lovekuangshen2"
2) "lovekuangshen"
3) "kuangshen"
127.0.0.1:6379> spop myset # 随机删除一些set集合中的元素！
"lovekuangshen2"
127.0.0.1:6379> spop myset
"lovekuangshen"
127.0.0.1:6379> SMEMBERS myset
1) "kuangshen"
```



## 4、修改

### 4.1 smove  

移动集合的元素

```bash

127.0.0.1:6379> sadd myset "hello"
(integer) 1
127.0.0.1:6379> sadd myset "world"
(integer) 1
127.0.0.1:6379> sadd myset "kuangshen"
(integer) 1
127.0.0.1:6379> sadd myset2 "set2"
(integer) 1
127.0.0.1:6379> smove myset myset2 "kuangshen" # 将一个指定的值，移动到另外一个set集合！
(integer) 1
127.0.0.1:6379> SMEMBERS myset
1) "world"
2) "hello"
127.0.0.1:6379> SMEMBERS myset2
1) "kuangshen"
2) "set2"
```



### 5、取交并差集

数字集合类：
- 差集 ： SDIFF
- 交集 ：SINTER 
- 并集 ： SUNION

keys1 : a b c    keys2 : c d e

```bash
127.0.0.1:6379> SDIFF key1 key2 # 差集
1) "b"
2) "a"
127.0.0.1:6379> SINTER key1 key2 # 交集 共同好友就可以这样实现
1) "c"
127.0.0.1:6379> SUNION key1 key2 # 并集
1) "b"
2) "c"
3) "e"
4) "a".
5) "d"
```



