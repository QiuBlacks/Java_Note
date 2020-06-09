

[TOC]



![image-20200515093220960](E:\black user\Java\有道云截图\image-20200515093220960.png)



## 一、Git关联Github项目



1、 绑定github用户名和邮箱

```java
$ git config --global user.name "BlackQiu"
$ git config --global user.email "1789729227@qq.com"
```

2、 生成密钥并查看复制

```
$ ssh-keygen -t rsa -C "1789729227@qq.com"
start ~/.ssh/id_rsa.pub
ssh -T git@github.com //检查是否连接成功
```

然后将密钥添加到GitHub里，点击Settings，选择SSH and GPG keys，接着点击 New SSH key，将之前复制的公钥填至对应Key一栏即可

3、创建本地仓库，并初始化

```
git init
```

4、关联

```java
 git remote add origin  url //添加远程仓库
 git push -u origin master //第一次将本地仓库上传至Github的仓库并进行关联
```



## 二、克隆项目

```java
git clone url  //url为项目服务器地址或github地址
```



## 三、更新项目

1、git status

2、git add file，file 为修改文件名

​	也可以 git add -A全部更新

3、git commit -m "heiheihei"	

4、 git push