# limit







## 二、优化

### 子查询优化法

先找出第一条数据，然后大于等于这条数据的 id 就是要获取的数据，要求数据连续，基本就是不能有 WHERE 语句了。

原始 SQL 语句：

```mysql
select * from Member limit 10000,100
```

优化后：

```mysql
select * from Member where MemberID >= (select MemberID from Member limit 100000,1) limit 100
```

### 使用 id 限定优化

这种方式假设数据表的 id 是连续递增的，则我们根据查询的页数和查询的记录数可以算出查询的 id 的范围，可以使用 id between and 来查询。

原始 SQL 语句：

```mysql
select * from orders_history where type=2 limit 100000,100
```

优化后：

```mysql
select * from orders_history where type=2 and id between 1000000 and 1000100 limit 100
```