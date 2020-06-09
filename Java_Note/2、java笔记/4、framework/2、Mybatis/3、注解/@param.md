[TOC]

## 一、基本介绍

### 1、作用

给每个==接口的参数==命名，作用于sql语句，使用#{}获取参数的值

### 2、实例

#### 1）Mapper实例

```java
List<CuxTodoItems> selectItembyparam1(@Param("id") int userId,@Param("pty") String Priority);
```

参数作用于sql

```java
  <select id="selectItembyparam1" resultMap="BaseResultMap">
      SELECT *
      FROM  cux_todo_items
      WHERE user_id = #{id}
      AND   priority = #{pty}
  </select>
```

#### 2）注解实例

有两个接口参数userId和roleId

```java
@Insert("insert into users_role(userId,roleId) values(#{userId},#{roleId})")
void addRoleToUser(@Param("userId") String userId, @Param("roleId") String roleId);
```



### 3、使用场景

用于==多个接口参数传入==，如果单个的话可以不用@Param



## 二、











## 参考：

​			[Mybatis传递多个参数的4种方式](https://blog.csdn.net/youanyyou/article/details/79406486)

​			







