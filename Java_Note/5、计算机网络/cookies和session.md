[TOC]

# Cookie和Session

## 一、cookie

### 1、出现背景

在网站中，http请求是无状态的。也就是说即使第一次和服务器连接后并且登录成功后，第二次请求服务器依然不能知道当前请求是哪个用户。

cookie的出现就是为了解决这个问题，第一次登录后服务器返回一些数据（cookie）给浏览器，然后浏览器保存在本地，当该用户发送第二次请求的时候，就会自动的把上次请求存储的cookie数据自动的携带给服务器，服务器通过浏览器携带的数据就能判断当前用户是哪个了。cookie存储的数据量有限，不同的浏览器有不同的存储大小，但一般不超过4KB。因此使用cookie只能存储一些小量的数据。

### 2、工作原理

（1）浏览器端第一次发送请求到服务器端
		（2）服务器端创建Cookie，该Cookie中包含用户的信息，然后将该Cookie发送到浏览器端
		（3）浏览器端再次访问服务器端时会携带服务器端创建的Cookie
		（4）服务器端通过Cookie中携带的数据区分不同的用户





## 二、session

### 1、基本介绍

session和cookie的作用有点类似，都是为了存储用户相关的信息。不同的是，cookie是存储在本地浏览器，而session存储在服务器。存储在服务器的数据会更加的安全，不容易被窃取。但存储在服务器也有一定的弊端，就是会占用服务器的资源，但现在服务器已经发展至今，一些session信息还是绰绰有余的。

### 2、工作原理

（1）浏览器端第一次发送请求到服务器端，服务器端创建一个Session，同时会创建一个特殊的Cookie（name为JSESSIONID的固定值，value为session对象的ID），然后将该Cookie发送至浏览器端
（2）浏览器端发送第N（N>1）次请求到服务器端,浏览器端访问服务器端时就会携带该name为JSESSIONID的Cookie对象
（3）服务器端根据name为JSESSIONID的Cookie的value(sessionId),去查询Session对象，从而区分不同用户。
			name为JSESSIONID的Cookie不存在（**关闭或更换浏览器**），返回1中重新去创建Session与特殊的Cookie
			name为JSESSIONID的Cookie存在，根据value中的SessionId去寻找session对象
			value为SessionId不存在**（Session对象默认存活30分钟）**，返回1中重新去创建Session与特殊的Cookie
			value为SessionId存在，返回session对象



## 三、重要知识点

### 1、异同

#### 1.1  同

cookie和session都是用来跟踪浏览器用户身份的会话方式（会话跟踪）

都是由服务端创建的

#### 1.2  异

​	session 在服务器端，cookie 在客户端（浏览器）

​	session比cookie安全



### 2、客户端禁止 cookie，session 还能用吗？

如果浏览器禁用了 cookie，浏览器请求服务器无法携带 sessionid，服务器无法识别请求中的用户身份，**session失效**。

但是可以**通过其他方法**在禁用 cookie 的情况下，**可以继续使用session**。

- 通过url重写，把 sessionid 作为参数追加的原 url 中，后续的浏览器与服务器交互中携带 sessionid 参数。
- 服务器的返回数据中包含 sessionid，浏览器发送请求时，携带 sessionid 参数。
- 通过 Http 协议其他 header 字段，服务器每次返回时设置该 header 字段信息，浏览器中 js 读取该 header 字段，请求服务器时，js设置携带该 header 字段。















