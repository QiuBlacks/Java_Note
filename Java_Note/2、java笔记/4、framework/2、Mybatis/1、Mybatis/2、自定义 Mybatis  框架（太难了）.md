# 自定义 Mybatis  框架（太难了）

# 一、自定义框架分析

mybatis在使用代理dao的方式实现增删改查时做什么事呢？

​		只有两件事：

​			第一：创建代理对象

​			第二：在代理对象中调用selectList



自定义mybatis流程图



![s's](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201016155042.png)



![自定义mybatis开发流程图 (1)](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910170034.png)



# 二、手写mybatis

参考https://blog.csdn.net/weixin_44450604/article/details/87468776