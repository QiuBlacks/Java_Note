[TOC]



## 参考：

 [Mysql常见面试题](https://github.com/TangBean/MarkdownNotes/blob/master/MySQL/MySQL%E5%B8%B8%E8%A7%81%E9%9D%A2%E8%AF%95%E9%A2%98.md)

[史上最全mySQL面试](https://www.nowcoder.com/discuss/135748?type=post&order=jing&pos=&page=1&channel=&source_id=1_post&subType=5)





### 一、什么是事务,为何用事务

保证数据的一致性和完整性



### 二、sql语句，怎样查找不重复的字段

#### 1、单字段使用Distinct关键字
  SELECT DISTINCT [字段名] FROM table

注意：这里DISTINCT后面只能加一个字段名

####  2、多字段使用GROUP BY 分组

### 三、悲观锁和乐观锁

### 四、处理大数据库的情况、方法步骤

### 五、数据库中的范式

### 六、最左前缀匹配原则及它的原因

### 七、索引在什么情况下会失效

1、 对于创建的多列索引（复合索引），不是使用的第一部分就不会使用索引

```mysql
alter table student add index my_index(name, age)   // name左边的列， age 右边的列                                                              
select * from student where name = 'aaa'     // 会用到索引
select * from student where age = 18          //  不会使用索引
```

2、对于like查询是以%开头，索引失效；以%结尾，索引有效

​		‘%aaa’ 不会使用索引，而 ‘aaa%’ 会使用到索引。

3、如果条件中有or，即使其中有条件带索引也不会使用(这也是为什么尽量少用or的原因)

注意：要想使用or，又想让索引生效，只能将or条件中的每个列都加上索引

4、 如果列类型是字符串，那么一定要在条件中使用引号引用起来，否则不使用索引

5、如果mysql认为全表扫面要比使用索引快，则不使用索引。

   如：表里只有一条数据。

