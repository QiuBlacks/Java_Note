# String



## 一、string的最大限制长度：

字符串有长度限制，在编译期，要求字符串常量池中的常量不能超过65535，并且在javac执行过程中控制了最大值为65534。

在运行期，长度不能超过Int的范围，否则会抛异常。



参考：[Hollis的String文章](https://mp.weixin.qq.com/s?__biz=MzI3NzE0NjcwMg==&mid=2650128788&idx=1&sn=b166e7bbc2792bce6e6835e8e4a6f9bf&chksm=f36bdcb5c41c55a3171d7b9cfbc637f014ece5036db378ab2cb10ebe8888c266690e2b1cb285&scene=126&sessionid=1589937532&key=ee7920faaaff2a8b93c9d05966d6c461ea5f3897f5e2475c3f56b82a1d6c9dc2c7680c3a1b93f3e01f2aabc5b34b15dfec47f48b82572f2d41a2eb1bb350ed17a124567376ebbc1dabb157f2422db18d&ascene=1&uin=MTg5MjE1NTkyOQ%3D%3D&devicetype=Windows+10+x64&version=6209007b&lang=zh_CN&exportkey=A1zsDDtl0QodfNyvpUvYJxQ%3D&pass_ticket=bvn6zszeDqk3OY9SoL0rKUnaFwIDPRfyYnVN4xYEATVEcM4WO%2B9hHkhpBpoW9Ilv)







### 字符型常量和字符串常量的区别?

1. 形式上: 字符常量是单引号引起的一个字符; 字符串常量是双引号引起的若干个字符
2. 含义上: 字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)
3. 占内存大小 字符常量只占 2 个字节; 字符串常量占若干个字节 (**注意： char 在 Java 中占两个字节**)