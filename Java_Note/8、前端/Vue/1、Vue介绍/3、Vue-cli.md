# vue-cli

# 一、基础知识

## 1、什么是vue-cli

  vue-cli官方提供的一个脚手架，用于快速生成一个vue的项目模板；
  预先定义好的目录结构及基础代码，就好比咱们在创建Maven项目时可以选择创建一个骨架项目，这个估计项目就是脚手架，我们的开发更加的快速；

## 2、项目的功能

- 统一的目录结构
- 本地调试
- 热部署
- 单元测试
- 集成打包上线



```
├── README.md      项目介绍
├── index.html      入口页面
├── build       构建脚本目录
│ ├── build-server.js     运行本地构建服务器，可以访问构建后的页面
│ ├── build.js      生产环境构建脚本
│ ├── dev-client.js     开发服务器热重载脚本，主要用来实现开发阶段的页面自动刷新

│ ├── dev-server.js     运行本地开发服务器

│ ├── utils.js      构建相关工具方法

│ ├── webpack.base.conf.js   wabpack基础配置

│ ├── webpack.dev.conf.js    wabpack开发环境配置

│ └── webpack.prod.conf.js   wabpack生产环境配置

├── config       项目配置

│ ├── dev.env.js      开发环境变量

│ ├── index.js      项目配置文件

│ ├── prod.env.js      生产环境变量

│ └── test.env.js      测试环境变量

├── mock       mock数据目录

│ └── hello.js

├── package.json     npm包配置文件，里面定义了项目的npm脚本，依赖包等信息

├── src        源码目录

│ ├── main.js       入口js文件

│ ├── app.vue       根组件

│ ├── components      公共组件目录

│ │ └── title.vue

│ ├── assets       资源目录，这里的资源会被wabpack构建

│ │ └── images

│ │  └── logo.png

│ ├── routes       前端路由

│ │ └── index.js

│ ├── store       应用级数据（state）状态管理

│ │ └── index.js

│ └── views       页面目录

│  ├── hello.vue

│  └── notfound.vue

├── static       纯静态资源，不会被wabpack构建。

└── test       测试文件目录（unit&e2e）

└── unit       单元测试

├── index.js      入口脚本

├── karma.conf.js     karma配置文件

└── specs       单测case目录

└── Hello.spec.js
```



# 二、vue教程

## vue3教程





























## vue2教程

存储位置：C:\Users\彼岸の烟火\AppData\Roaming\npm\node_modules

## 1、安装vue-cli

安装Node.js：http://nodejs.cn/download/

安装cnmp  ：npm install -g cnpm --registry=https://registry.npm.taobao.org

安装vue  ：  cnpm install vue

安装vue-cli  ：cnpm install -g vue-cli

![image-20201108162827038](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201108162835.png)





## 2、第一个vue-cli应用程序

1) 创建一个Vue项目，我们随便建立一个空的文件夹在电脑上，我这里在F盘下新建一个目录

```
F:\WorkSpace\Vue
```

2) 创建一个基于webpack模板的vue应用程序

```bash
#1、首先需要进入到对应的目录 cd D:\Project\vue-study
#2、这里的myvue是顶日名称，可以根据自己的需求起名
vue init webpack myvue
```

 一路都选择no即可；

**说明：**

- Project name：项目名称，默认回车即可
- Project description：项目描述，默认回车即可
- Author：项目作者，默认回车即可
- Vue build (Use arrow keys)：选择第一个即可
- Install vue-router：是否安装vue-router，选择n不安装（后期需要再手动添加）
- Use ESLint to lint your code:是否使用ESLint做代码检查，选择n不安装（后期需要再手动添加)
- Set up unit tests:单元测试相关，选择n不安装（后期需要再手动添加）
- Setupe2etests with Nightwatch：单元测试相关，选择n不安装（后期需要再手动添加）
- Should we run npm install for you after the,project has been created:创建完成后直接初始化，选择n，我们手动执行；运行结果！

3)初始化并运行

```
cd myvue
npm install
npm run dev
```

4) 访问     http://localhost:8080/



