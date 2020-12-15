# Executor执行器

# 一、基础知识

## 1、基本介绍

Executor 是mybatis 执行sql的接口。所有的增删改查，以及其他操作，最终都会由Executor 来执行。它功能主要包括

- 基本功能： 增，删，改，查
- 缓存维护： 执行器Executor 主要维护的是1级缓存服务，主要包括创建缓存，清理缓存，判断缓存是否存在
- 事务管理： 事务提交，回滚，关闭，批处理更新



## 2、实现类

Mybatis有三种基本的Executor执行器:  SimpleExecutor、ReuseExecutor、BatchExecutor。

### 2.1 SimpleExecutor

  每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

```xml
[2019-06-13 11:30:38:812][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==>  Preparing: insert into t_student values (null , ?, ?, ?, ?) 
[2019-06-13 11:30:38:819][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_0(String), 20(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:30:38:824][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1

#第二次执行
[2019-06-13 11:30:38:827][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==>  Preparing: insert into t_student values (null , ?, ?, ?, ?) 
[2019-06-13 11:30:38:828][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_1(String), 21(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:30:38:832][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1

[2019-06-13 11:30:38:833][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==>  Preparing: insert into t_student values (null , ?, ?, ?, ?) 
[2019-06-13 11:30:38:839][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_2(String), 22(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:30:38:841][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1
...
耗时:21575 ms!
```

### 2.2 ReuseExecutor

执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map内，供下一次使用。简言之，就是重复使用Statement对象。

从执行日志可以看出, **只有第一次插入操作, 执行了sql编译步骤**, 对其它插入操作执行了设置参数, 执行sql的操作.

```
[2019-06-13 11:31:11:752][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==>  Preparing: insert into t_student values (null , ?, ?, ?, ?) 

[2019-06-13 11:31:11:757][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_0(String), 20(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:31:11:759][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1

[2019-06-13 11:31:11:761][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_1(String), 21(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:31:11:764][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1

[2019-06-13 11:31:11:776][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_2(String), 22(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:31:11:778][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- <==    Updates: 1
...
耗时:19322 ms!
```



### 2.3 BatchExecutor

执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

从执行日志可以看出, 只对第一次插入操作执行了sql编译操作, 对其它插入操作仅执行了设置参数操作, 最后统一执行

```sql
[2019-06-13 11:31:29:270][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==>  Preparing: insert into t_student values (null , ?, ?, ?, ?) 
[2019-06-13 11:31:29:276][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_0(String), 20(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:31:29:277][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_1(String), 21(Integer), M(String), 2019-06-13(Date)
[2019-06-13 11:31:29:277][main][DEBUG][o.z.l.m.l.mapper.StudentMapper.save]- ==> Parameters: zhangsan_2(String), 22(Integer), M(String), 2019-06-13(Date)
...
耗时:835 ms!
```



## 3、作用范围

Executor的这些特点，都严格限制在**SqlSession生命周期**范围内。



## 4、配置Executor执行器

在Mybatis配置文件中，在设置（settings）可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数，如SqlSession openSession(ExecutorType execType)。

配置默认的执行器。SIMPLE 就是普通的执行器；REUSE 执行器会重用预处理语句（prepared statements）； BATCH 执行器将重用语句并执行批量更新。

### 4.1、SqlSession设置

在获取sqlSession时设置, 需要注意的时, 如果选择的是批量执行器时, 需要手工提交事务.

```
// 获取指定执行器的sqlSession
SqlSession sqlSession = sqlSessionFactory.openSession(ExecutorType.BATCH)

// 获取批量执行器时, 需要手动提交事务
sqlSession.commit();
```



```
<!--配置一个可以进行批量执行的sqlSession  -->
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
    <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactoryBean"></constructor-arg>
    <constructor-arg name="executorType" value="BATCH"></constructor-arg>
</bean>
```



### 4.2settings配置（不推荐）

可在**全局配置文件**中配置, 但是笔者不推荐这种方式, 了解即可.

```xml
<settings>
    <setting name="defaultExecutorType" value="BATCH" />
</settings>
```







# 三、



## 参考文章

[Mybatis 的三种执行器](https://blog.csdn.net/zongf0504/article/details/100104029)