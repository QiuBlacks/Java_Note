

# JSTL:比EL更强大

# **一、准备工作：**

**1）导入两个jar包：jstl.jar     standard.jar** 

**2）引入taglib** 

<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %> 其中 prefix="c"的c是前缀 

# **二、核心标签库**

## **1、通用标签库**

** 复制**

**i:在某个作用域里，给某个变量赋值**

```
    <c:set var="salary" scope="session" value="value"/>   
    相当于:   request.setAttribute("value","salary")
```

  **ii:在某个作用域中，给某个对象的属性赋值(不能使用scope)**

```
    <c:set target="${requestScope.student}" property="uname" value="李白"/>
```

​        **ps:可以给不存在的变量创建赋值，对象不可以**

```
    <c:out> 显示 
    <c:out value="<string>"  default="<string>"  escapeXml="<true|false>"/>
```

​    **escapeXml:	是否忽略XML特殊字符**

```
<c:out value='<a herf="http://baidu.com">百度</a> ' default="<string>"  escapeXml="<true>"/>       
<c:out value='<a herf="http://baidu.com">百度</a> ' default="<string>"  escapeXml="<false>"/>
```



## 2、条件标签库

```
<c:if test="<boolean>" var="<string>" scope="<string>">
//test字符串后面不能加空格，否则返回false 
<c:if test="<boolean>   "  var="<string>" scope="<string>"> 
```

还有很多，详见菜鸟教程

## 3、迭代标签库(循环)

```
<c:forEach>  
    items="<object>"  
    begin="<int>"   
    end="<int>"  
    step="<int>"  
    var="<string>" 
    varStatus="<string>"
>
```

注意：所有变量字符串里注意别多加空格