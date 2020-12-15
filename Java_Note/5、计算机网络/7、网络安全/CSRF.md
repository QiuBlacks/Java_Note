## CSRF

Cross Site Request Forgery, 跨站域请求伪造

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201106154536.webp)

从上图可以看出，要完成一次CSRF攻击，受害者必须依次完成两个步骤：

- 登录受信任网站A，并在本地生成Cookie。
- 在不登出A的情况下，访问危险网站B。
   看到这里，你也许会说：“如果我不满足以上两个条件中的一个，我就不会受到CSRF的攻击”。是的，确实如此，但你不能保证以下情况不会发生：


https://www.jianshu.com/p/7f33f9c7997b
