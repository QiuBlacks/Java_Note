## 目录

[TOC]







## 一、idea乱码

1、file-setting 搜索encoding ,改成UTF-8

2、打开idea工作目录bin,在idea64.exe.vmoption和idea.exe.vmoption里面加-Dfile.encoding=UTF-8

3、配置tomcat页面的：VM option设置-Dfile.encoding=UTF-8



## 二、**导入jar包**：

1、java项目：直接复制到工程中，右键add as library

2、web项目： 像eclipse一样导包，jar包只在运行时有效

​    ·解决方案： gradle/maven

手工解决(了解)： 1) 只在运行时有效

在web创建lib，复制jar包即可

Structure-Artifacts-output导入jar

2）开发时有效（需要用到jar包里的函数）

完成1）后，将jar复制到工程src，右键 add as library

推荐方法：file-project Structure - libbraries导入项目里的lib文件



## 三、**删除项目：**

https://blog.csdn.net/daponi/article/details/95328834