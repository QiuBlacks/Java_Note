# 一、判断配置方法

Mybatis基于xml配置还是注解，看是resource还是class

```
<mappers>
    <mapper resource="com/qiu/dao/IUserDao.xml"/>
</mappers>
```



```
<!-- 指定映射配置文件的位置，映射配置文件指的是每个dao独立的配置文件 -->
    <mappers>
        <mapper class="com.itheima.dao.IUserDao"/>
    </mappers>
```



# 二、Mybatis中的 ${} 和 #{}区别与用法

1、举例说明

```
 select  from user where name = "dato"; 
 select  from user where name = #{name};   
 select  from user where name = '${name}'; 
```

2、使用区别

动态 SQL 是 mybatis 的强大特性之一，也是它优于其他 ORM 框架的一个重要原因。mybatis 在对 sql 语句进行预编译之前，会对 sql 进行动态解析，解析为一个 BoundSql 对象，也是在此处对动态 SQL 进行处理的。在动态 SQL 解析阶段， #{ } 和 ${ } 会有不同的表现

\#{} 在动态解析的时候， 会解析成一个参数标记符。就是解析之后的语句是：

```
select  from user where name = ？; 
```

${}在动态解析的时候，会将我们传入的参数当做String字符串填充到我们的语句中，就会变成下面的语句

```
select  from user where name = "dato"; 
```

预编译之前的 SQL 语句已经不包含变量了，完全已经是常量数据了。相当于我们普通没有变量的sql了。

3、还有以下区别：

- \#方式能够很大程度防止sql注入
- $方式无法防止Sql注入。
- $方式一般用于传入数据库对象，例如传入表名
- 一般能用#的就别用$.



# 三、