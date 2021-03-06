# B+Tree

## 一、基本介绍

### 1、定义

B+ 树是 B 树的变种，有着比 B 树更高的查询效率

### 2、特征

1）有m个子结点的的节点的关键字有m个；

2）非叶节点仅具有索引的作用，所有数据（全部关键字的信息，及指向含这些关键字记录的指针）都保存在叶子节点

3）树的所有叶子结点构成一个有序链表，可以按照关键码排序的次序遍历全部记录。







## 二、B+树数据存储

### 1、基本介绍

InnoDB中所有数据文件（后缀为ibd的文件），大小始终都是**16384**（16k）的整数倍。

InnoDB存储引擎的最小储存单元，页（Page），**一个页的大小是16K**。

在B+树中**叶子节点存放数据**，**非叶子节点**存放**键值+指针**。



### 2、节点存储数据大小

InnoDB中使用B+树存储数据，**一个节点的大小为一个页**

#### 2.1 叶子节点

 **节点指针数 * 单个叶子节点记录行数**

一般来说，单个叶子节点记录行数 = 一个页大小 / 每行数据大小 = 16 / 1 k = 16kb



#### 2.2 非叶子结点

非叶子节点存储（索引值+指针）个数  ==  键值8b （bigint类型） +     指针（6b） =  **14 b**

所以，非叶子节点（索引值+指针）个数 = 单个节点大小  /  14b 

​																	= 16k / 14b  = 16 * 1024 / 14 = **1170 个**





### 3、实例

以单个叶子节点的每行数据大小为1k为例

一棵高度为2的B+树，能存放1170  *  16= 18720条这样的数据记录。

一棵高度为3的B+树，能存放1170  *  1170  * 16= 21902400 （2kw）条这样的数据记录。

即**一棵B+树存储的数据行数 =  叶子节点上一层的（索引值+指针）个数    *  单个叶子节点记录行数**







## 参考文章

[mysql为什么使用B+树，B+树可以存储多少行数据](https://www.jianshu.com/p/0aba6d552a55)







