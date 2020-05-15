# **自定义 Mybatis  框架（太难了）**

# **一、自定义框架分析**

mybatis在使用代理dao的方式实现增删改查时做什么事呢？

​		只有两件事：

​			第一：创建代理对象

​			第二：在代理对象中调用selectList



**自定义mybatis流程图![2cf31ea038b2e990f4ef9af6ea9f4f77_WEBRESOURCE44c190dd1f88fdbea8d37a5519526fd5](E:\black user\Java\有道云截图\2cf31ea038b2e990f4ef9af6ea9f4f77_WEBRESOURCE44c190dd1f88fdbea8d37a5519526fd5.png)**



![自定义mybatis开发流程图 (1)](E:\black user\Java\有道云截图\自定义mybatis开发流程图 (1).png)

# **二、手写mybatis**

参考https://blog.csdn.net/weixin_44450604/article/details/87468776