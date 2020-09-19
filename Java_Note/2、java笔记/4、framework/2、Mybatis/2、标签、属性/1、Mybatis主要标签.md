# Mybatis主要标签

![image-20200513134758614](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170057.png)





# 一、定义SQL语句

## 1、select

selectkey

　　（1） 属性

　　　 　id :唯一的标识符.

　　　 　parameterType:传给此语句的参数的全路径名或别名 例:com.test.poso.User或user

　　　 　resultType :语句返回值类型或别名。注意，如果是集合，那么这里填写的是集合的泛型，而不是集合本身（resultType 与resultMap 不能并用）

　 　例子:

```
<select id="userList" parameterType="user" resultType="User">   
	select  from user where name =#{name}
</select>
```



## 2、insert 

　　　　属性介绍:

　　　　　　id :唯一的标识符

　　　　　　parameterType:传给此语句的参数的全路径名或别名 例:com.test.poso.User

## 3、delete

　

```
<delete id="deleteUser" parameterType="int"> 　
   	delete from user where id = #{id} 　　
</delete>
```



## 4、update

　　　　类似于insert