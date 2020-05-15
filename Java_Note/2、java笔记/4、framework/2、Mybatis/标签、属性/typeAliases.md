# **typeAliases**

## 一、：标签介绍

在 MyBatis 的 sql 映射配置文件中，需要使用 paramterType、resultType 来设置 sql 语句的输入输出参数，一般参数都是基本的数据类型或封装类型，但都需要声明该类型的全路径，java.lang.String，或者 cn.com.mybatis.pojo.User, 这时就可以通过 typeAliases 别名来设置，简化复杂度

```
<typeAliases>    
	<typeAlias alias="user" type="cn.com.mybatis.pojo.User"/>
</typeAliases>
```

使用package：程序会为包下的所有类都自动加上别名，其定义别名的规范就是对应包装类的类名首字母变为小写

```
 <!--使用typeAliases配置别名，它只能配置domain中类的别名 -->  
 <typeAliases>      
	 <package name="com.itheima.domain"></package>  
 </typeAliases>
```

