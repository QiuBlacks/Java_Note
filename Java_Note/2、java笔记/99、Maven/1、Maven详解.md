# **Maven详解**

# **一、基本介绍**

## **1、概念**

Maven就是一款帮助程序员构建项目的工具

## **2、****Maven基本命令**

- -v:查询Maven版本 
- compile：编译 
- test:[测试](http://lib.csdn.net/base/softwaretest)项目 
- package:打包 
- clean:删除target文件夹
- install:安装 

## **3、****Maven仓库**

Maven仓库用来存放Maven管理的所有Jar包。分为：本地仓库 和 中央仓库，远程仓库（公司自己的）

- 本地仓库 

Maven本地的Jar包仓库。

- 中央仓库 

Maven官方提供的远程仓库。

## **4、****坐标**

坐标是Jar包的唯一标识，Maven通过坐标在仓库中找到项目所需的Jar包。 

- - groupId:所需Jar包的项目名
  - artifactId:所需Jar包的模块名
  - version:所需Jar包的版本号

## 5、

<dependencyManagement> ：可以锁定jar包版本，但不会导入依赖