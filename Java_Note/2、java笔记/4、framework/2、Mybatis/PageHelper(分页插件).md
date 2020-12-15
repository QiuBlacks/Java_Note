# PageHelper(分页插件)

# 一、基本介绍

## 1、PageHelper介绍

PageHelper是国内非常优秀的一款开源的mybatis分页插件，它支持基本主流与常用的数据库，例如mysql、

oracle、mariaDB、DB2、SQLite、Hsqldb等。

本项目在 github 的项目地址：https://github.com/pagehelper/Mybatis-PageHelper

本项目在 gitosc 的项目地址：http://git.oschina.net/free/Mybatis_PageHelper





## 2、常用参数

PageHelper 的参数比较多, 但很多参数, 保持默认即可.

- offsetAsPageNum：默认值为 false, 该参数会将传入的RowBounds 中的offset当做是pageNum 使用.
- rowBoundsWithCount：默认值为false, 当该参数设置为true时, 使用 RowBounds 分页会进行 count 查询。
- reasonable：分页合理化参数，默认值为false. 当该参数设置为 true 时, pageNum<=0 时会查询第一页; pageNum>pages（超过总数时）,会查询最后一页.





### 工作原理

Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

举例：select * from student，拦截sql后重写为：select t.* from (select * from student) t limit 0, 10







pageHelper会使用**ThreadLocal**获取到同一线程中的变量信息，各个线程之间的Threadlocal不会相互干扰，也就是Thread1中的ThreadLocal1之后获取到Tread1中的变量的信息，不会获取到Thread2中的信息

所以在多线程环境下，**各个Threadlocal之间相互隔离**，可以实现，不同thread使用不同的数据源或不同的Thread中执行不同的SQL语句

所以，PageHelper利用这一点通过**拦截器**获取到同一线程中的预编译好的SQL语句之后**将SQL语句包装成具有分页功能的SQL语句**，并将其再次赋值给下一步操作，所以实际执行的SQL语句就是有了分页功能的SQL语句



- PageHelper 采用了Mybatis 自定义插件技术, 拦截的是Executor 类
- PageHelper 会将分页参数存为ThreadLocal 变量中, 当执行过任意一次查询后, 会清理. 因此对于以下用法, 可能会导致线程安全问题.

```java
PageHelper.startPage(1, 10);
List<Country> list;
if(param1 != null){
    list = countryMapper.selectIf(param1);
} else {
    list = new ArrayList<Country>();
}
```







# 二、集成PageHelper

### 1、引入依赖

```
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.1.10</version>
</dependency>
```



### 2、配置插件

```
<plugins>
    <plugin interceptor="com.github.pagehelper.PageInterceptor" >
        <property name="offsetAsPageNum" value="true"/>
        <property name="rowBoundsWithCount" value="true"/>
        <property name="reasonable" value="true"/>
    </plugin>
</plugins>
```



## 3、测试

### 3.1 接口定义

```java
// RowBounds 中offset 为页码, 而非偏移量
List<StudentPO> queryAll(RowBounds rowBounds);

// 分页查询, 带参数
List<StudentPO> queryBySex(@Param("sex") String sex, RowBounds rowBounds);
```

### 3.2 sql 映射文件

```java
<!-- 无条件查询 -->
<select id="queryAll" resultType="StudentPO">
    select * from t_student
</select>

<!-- 带条件查询 -->
<select id="queryBySex" resultType="StudentPO">
    select * from t_student where sex = #{sex}
</select>
```

### 3.3 测试用例

```java
// 测试无条件分页
@Test
public void queryAll(){
    StudentMapper mapper = SqlSessionUtil.getMapper(StudentMapper.class);
    List<StudentPO> studentPOS = mapper.queryAll(new RowBounds(2, 10));
    PageInfo<StudentPO> pageInfo = new PageInfo(studentPOS);
    System.out.println(pageInfo);
}

// 测试有条件分页
@Test
public void queryBySex(){
    StudentMapper mapper = SqlSessionUtil.getMapper(StudentMapper.class);
    List<StudentPO> studentPOS = mapper.queryBySex("M", new RowBounds(2, 10));
    PageInfo<StudentPO> pageInfo = new PageInfo(studentPOS);
    System.out.println(pageInfo);
}
```









