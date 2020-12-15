# webpack

# 一、基础知识

## 1、什么是Webpack

本质上， webpack是一个现代JavaScript应用程序的**静态模块打包器**(module bundler) 。当webpack处理应用程序时， 它会递归地构建一个依赖关系图(dependency graph) ， 其中包含应用程序需要的每个模块， 然后将所有这些**模块打包成一个或多个bundle**
  Webpack是当下最热门的前端资源模块化管理和打包工具， 它可以将许多松散耦合的模块按照依赖和规则打包成符合生产环境部署的前端资源。还可以将按需加载的模块进行代码分离，等到实际需要时再异步加载。通过loader转换， 任何形式的资源都可以当做模块， 比如Commons JS、AMD、ES 6、CSS、JSON、Coffee Script、LESS等；



# 二、Webpack配置

## 1、安装Webpack

```javascript
npm install webpack -g
npm install webpack-cli -g
```

  测试安装成功

- `webpack -v`
- `webpack-cli -v`



## 2、配置

  创建 `webpack.config.js`配置文件

- entry：入口文件， 指定Web Pack用哪个文件作为项目的入口
- output：输出， 指定WebPack把处理完成的文件放置到指定路径
- module：模块， 用于处理各种类型的文件
- plugins：插件， 如：热更新、代码重用等
- resolve：设置路径指向
- watch：监听， 用于设置文件改动后直接打包

```js
module.exports = {
	entry:"",
	output:{
		path:"",
		filename:""
	},
	module:{
		loaders:[
			{test:/\.js$/,;\loade:""}
		]
	},
	plugins:{},
	resolve:{},
	watch:true
}
```

  直接使用 webpack 命令打包模块



# 三、测试

我们就可以将一个个模块打包成出口JS文件，使用时再一个个导入，实现了vue的模块化

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201108211615.png" alt="image-20201108211611889" style="zoom: 150%;" />

1、创建项目

2、创建一个名为modules的目录，用于放置JS模块等资源文件

3、在modules下创建**模块文件**，如hello.js，用于编写JS模块相关代码

```js
	//暴露一个方法：sayHi
	exports.sayHi = function(){
		document.write("<div>Hello Webpack</div>");
	}
```

4、在modules下创建一个名为main.js的**入口文件**，用于打包时设置entry属性

```js
//require 导入一个模块，就可以调用这个模块中的方法了
var hello = require("./hello");
hello.sayHi();
```

5、在项目目录下创建webpack.config.js**配置文件**，使用webpack命令打包

```js
module.exports = {
	entry:"./modules/main.js",
	output:{
		filename:"./js/bundle.js"
	}
}
```

6、在项目目录下创建HTML页面，如index.html，导入webpack打包后的JS文件

```html
<!doctype html>
<html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>狂神说Java</title>
    </head>
    <body>
        <script src="dist/js/bundle.js"></script>
    </body>
</html>
```

7、在IDEA控制台中直接执行webpack；如果失败的话，就使用管理员权限运行即可！

8、运行HTML看效果