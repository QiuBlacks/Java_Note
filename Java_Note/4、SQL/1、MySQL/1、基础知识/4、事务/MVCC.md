# MVCC：多版本并发控制

# 一、基本介绍

## 1、基础知识

MVCC: Multiversion Concurrency Control，翻译为多版本并发控制，其目标就是为了提高数据库在高并发场景下的性能。

**MVCC最大的优势：读不加锁，读写不冲突。在读多写少的场景下极大的增加了系统的并发性能**



InnoDB相比与MYISAM的提升就是对于行级锁的支持和对事务的支持，而应对高并发事务, `MVCC` 比单纯的加行锁更有效, 开销更小。

在不同的隔离级别下，数据库通过 `MVCC` 和隔离级别，让事务之间并行操作遵循了某种规则，来保证单个事务内前后数据的一致性。



## 2、实现原理

主要是依赖undo日志 ，Read View 来实现的

## 3、实现目标

- **读取已提交read committed (RC)**

- **可重复读repeatable read（RR）**



而未提交读隔离级别总是读取最新的数据行，要求很低，无需使用 MVCC。

可串行化隔离级别需要对所有读取的行都加锁，单纯使用 MVCC 无法实现。



# 二、Undo Log 日志

在InnoDB中MVCC的实现通过两个重要的字段进行连接：DB_TRX_ID和DB_ROLL_PT，在多个事务并行操作某行数据的情况下，不同事务对该行数据的UPDATE会产生多个版本，数据库通过**DB_TRX_ID**来标记版本，然后用**DB_ROLL_PT回滚指针**将这些版本以先后顺序连接成一条 **Undo Log 链**。

![Undo log](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200913105131.png)

## 1、属性

- DB_TRX_ID: 事务id，6byte，每处理一个事务，值自动加一。


> InnoDB中每个事务有一个唯一的事务ID叫做 transaction id。在事务开始时向InnoDB事务系统申请得到，是按申请顺序严格递增的
>
> 每行数据是有多个版本的，每次事务更新数据时都会生成一个新的数据版本，并且把transaction id赋值给这个数据行的DB_TRX_ID

- DB_ROLL_PT: 回滚指针，7byte，指向当前记录的ROLLBACK SEGMENT 的undolog记录，通过这个指针获得之前版本的数据。该行记录上所有旧版本在 undolog 中都通过链表的形式组织。
- DB_ROW_ID(隐含id,6byte，由innodb自动产生)，如果数据表没有主键，InnoDB会自动以DB_ROW_ID产生一个聚簇索引



我们可能听说过InnoDB下聚簇索引B+Tree的构造规则:

> 如果声明了主键，InnoDB以用户指定的主键构建B+Tree，如果未声明主键，InnoDB 会自动生成一个隐藏主键，说的就是`DB_ROW_ID`。另外，每条记录的头信息（record header）里都有一个专门的`bit`（deleted_flag）来表示当前记录是否已经被删除

## 2、分类

### insert undo log

代表事务在insert新记录时产生的undo log, 只在事务回滚时需要，并且在事务提交后可以被立即丢弃

### update undo log

 事务在进行update或delete时产生的undo log; 不仅在事务回滚时需要，在快照读(select，当读的过程中有写的十事务开始和提交，会造成读数据的脏读、不可重复读、幻读等)时也需要；所以不能随便删除，只有在快速读或事务回滚不涉及该日志时，对应的日志才会被purge线程统一清除



## 3、Update具体流程

事务 A 对值 x 进行更新之后，该行即产生一个新版本和旧版本。假设之前插入该行的事务 ID 为 100，事务 A 的 ID 为 200，该行的隐藏主键为 1

![preview](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200913110229.jpeg)

事务 A 的操作过程为：

1. 对 DB_ROW_ID = 1 的这行记录加**排他锁**
2. 把该行原本的值拷贝到 undo log 中，DB_TRX_ID 和 DB_ROLL_PTR 都不动
3. 修改该行的值这时产生一个新版本，更新 DATA_TRX_ID 为修改记录的事务 ID，将 DATA_ROLL_PTR 指向刚刚拷贝到 undo log 链中的旧版本记录，这样就能通过 DB_ROLL_PTR 找到这条记录的历史版本。如果对同一行记录执行连续的 UPDATE，Undo Log 会组成一个链表，遍历这个链表可以看到这条记录的变迁
4. 记录 redo log，包括 undo log 中的修改

redo log：记录了对实际数据文件的物理变更(数据文件的什么位置数据做了如何的变更)



# 三、ReadView：一致性读视图

## 1、ReadView生成时机

在read committed级别下，readview会在事务中的**每一个SELECT语句查询发送前生成**（也可以在声明事务时显式声明START TRANSACTION WITH CONSISTENT SNAPSHOT），因此每次SELECT都可以获取到当前已提交事务和自己修改的最新版本。

而在repeatable read级别下，每个事务只会在**第一个SELECT语句查询发送前**或**显式声明处**生成，其他查询操作都会基于这个ReadView，这样就保证了一个事务中的多次查询结果都是相同的，因为他们都是基于同一个ReadView下进行MVCC机制的查询操作。



## 2、查询操作流程

InnoDB为每一个事务构造了一个数组m_ids用于保存一致性视图生成瞬间当前所有活跃事务**(开始但未提交事务)的ID**，将数组中事务**ID最小值记为低水位m_up_limit_id**，当前系统中已创建事务**ID最大值+1记为高**水位m_low_limit_id，构成如图所示:

![image-20200913103947596](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200913103954.png)

一致性视图下查询操作的流程如下:

当查询发生时根据以上条件生成ReadView，该查询操作遍历Undo log链，根据当前被访问版本(可以理解为Undo log链中每一个记录即一个版本，遍历都是从最新版本向老版本遍历)的DB_TRX_ID。

1）如果**DB_TRX_ID小于m_up_limit_id,**则该版本在**ReadView生成前就已经完成提交**，该版本可以被当前事务访问。DB_TRX_ID在绿色范围内的**可以被访问**

2）若被访问版本的**DB_TRX_ID大于m_low_limit_id**，说明该版本在ReadView生成之后才生成，因此该版本**不能被访问**，根据当前版本指向上一版本的指针DB_ROLL_PT访问上一个版本，继续判断。DB_TRX_ID在蓝色范围内的都不允许被访问

3）若被访问版本的**DB_TRX_ID在[m_up_limit_id, m_low_limit_id)区间**内，则判断DB_TRX_ID是否**存在在当前所有活跃事务的集合ID里**，等于则证明是当前事务做的修改，可以被访问，否则不可被访问, 继续向上寻找。只有DB_TRX_ID等于当前事务ID才允许访问橙色范围内的版本。

需要根据隔离级别再进行判断：

- 提交读：如果 TRX_ID 在 TRX_IDs 列表中，表示该数据行快照对应的事务还未提交，则该快照不可使用。否则表示已经提交，可以使用。
- 可重复读：**都不可以使用。**因为如果可以使用的话，那么其它事务也可以读到这个数据行快照并进行修改，那么当前事务再去读这个数据行得到的值就会发生改变，也就是出现了不可重复读问题。

最后，还要确保满足以上要求的可访问版本的数据的delete_flag不为true，否则查询到的就会是删除的数据。



具体案例：参考 https://www.cnblogs.com/sunjingwu/p/12386660.html





## 参考文章

[MySQL InnoDB MVCC机制吐血总结](https://www.jianshu.com/p/d67f0329d3bf)

[MySQL InnoDB MVCC 机制的原理及实现](https://zhuanlan.zhihu.com/p/64576887)