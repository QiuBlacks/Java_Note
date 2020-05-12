# EL(Expression Language)
传统的JSP代码弊端：类型转化 、需要处理Null、代码参杂 <br/>
代替JSP页面中复杂的JAVA代码.

## 一、 EL表达式格式： 
### 1、点操作符--使用方便，包含特殊字符返回空值
```
${域对象.域对象的属性.属性.级联属性}
${requestScope.student.sno}
```
### 2、[ ]操作符--功能强大，可以包含特殊字符
#### 1) [" "]表示属性
```
${requestScope.student.["sno"]}
```
#### 2) [ ]可以加属性变量
```
int no=sno;
${requestScope.student.[no]}
```
####  3) 获取数组
```
${requestScope.hobbies[i]}
```
## 二、用途：
1、获取Map属性
```
Map<Stirng,Object> map=new HashMap<>();
    map.put("cn","中国");
    map.put("us","美国");
    requset.setAttribute("map",map);
    
${requestScope.map.cn};
${requestScope.map["us"]};
```
## 二、运算符
1.算术运算符有五个：+、-、*或$、/或div、%或mod <br/>
2.关系运算符有六个：==或eq、!=或ne、<或lt、>或gt、<=或le、>=或ge<br/>
3.逻辑运算符有三个：&&或and、||或or、!或not<br/>
4.其它运算符有三个：Empty运算符、条件运算符、()运算符<br/>
**Empty：判断是否为空或者null**:
${empty  值 }
## 三、隐式对象(不需要New就能使用的对象，自带的对象)

### 1)与范围有关的隐含对象
```
与范围有关的EL 隐含对象包含以下四个：<br/>
pageScope、requestScope、sessionScope和applicationScope；<br/>
    例子： ${sessionScope.username}<br/>
如果不指定域对象，则默认会根据从小到大的顺序依次取值
 ```
 
### 2)参数访问对象：获取表单数据
```
${param}:  用于获取请求参数的值，应用在参数值只有一个的情况，返回结果为字符串
${paramValues}:如果一个请求参数名对应多个值，则需要使用paramValues对象获取请求参数的值，该对象返回的结果为数组

ps:${param.username}
    ${paramValues}
```

### 3）JSP隐含对象
```
1、pageContext:  获取其他JSP隐含对象 
${pageContext.方法名}
例： ${pageContext.session} 由${pageContext.getSession()}得来
2、获取其他JSP隐含对象的级联方法的方法
${pageContext.request.serverport}
```

### 4)其他隐含对象

**1)cookie**   <br/>
JSTL并没有提供设定cookie的动作，
例：要取得cookie中有一个设定名称为userCountry的值，可以使用${cookie.userCountry} 来取得它。<br/>
**2)header和headerValues**<br/>
header 储存用户浏览器和服务端用来沟通的数据例：<br/>
要取得用户浏览器的版本，可以使用${header["User-Agent"]}。另外在鲜少机会下，有可能同一标头名称拥有不同的值，此时必须改为使用headerValues 来取得这些值。<br/>
**3)initParam**<br/>
initParam取得设定web站点的环境参数(Context)<br/>
```
例：一般的方法
String userid=(String)application.getInitParameter("userid");

可以使用${initParam.userid}来取得名称为userid
```
**4)pageContex**t<br>
pageContext取得其他有关用户要求或页面的详细信息。<br/>
