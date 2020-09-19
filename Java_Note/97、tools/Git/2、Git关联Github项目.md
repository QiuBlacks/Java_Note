

[TOC]



![image-20200515093220960](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910183623.png)



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

3、git commit -m "java_note_update"	

4、 git push



## 四、忽略文件上传



创建  .gitignore  文件，在里面编写：

| 命令        | 作用                                                   |
| ----------- | ------------------------------------------------------ |
| log/        | 忽略log下的所有文件                                    |
| /a.txt      | 仅忽略项目根目录下的 a.txt，不包括 Game/a.txt          |
| build/*.txt | 忽略 build下一级所有的.txt，但不包括 build/debug/a.txt |
| !a.exe      | 但 a.exe 除外                                          |
| *.exe       | 忽略所有 .exe 结尾的文件                               |



但是通常配置完后不会生效，因为.gitignore只能忽略那些原来没有被track的文件，如果某些文件已经被纳入了版本管理中，则修改.gitignore是无效的。那么解决方法就是先把本地缓存删除（改变成未track状态），然后再提交：

```
git rm -r --cached 
 git add -A
git commit -m '×××××'
git push
```

