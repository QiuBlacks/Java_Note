# solt插槽: 内容分发

# 一、基础知识







# 二、测试

比如准备制作一个待办事项组件(todo) ， 该组件由待办标题(todo-title) 和待办内容(todo-items)组成，但这三个组件又是相互独立的，该如何操作呢?

### 1、定义一个待办事项的组件

```html
<div id="app">
    <todo></todo>
</div>
<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    Vue.component('todo',{
        template:'<div>\
                <div>代办事项</div>\
                <ul>\
                    <li>学习狂神说Java</li>\
                </ul>\
            </div>'
    })
</script>
123456789101112131415
```

### 2、solt动态绑定

我们需要让代办事项的标题和值实现**动态绑定**，怎么做呢？我们可以留一个插槽！

2.1 将上面的代码留出一个插槽，即slot

```html
 Vue.component('todo',{
        template:'<div>\
                <slot name="todo-title"></slot>\
                <ul>\
                    <slot name="todo-items"></slot>\
                </ul>\
            </div>'
    });
12345678
```

2.2 定义一个名为todo-title的待办标题组件 和 todo-items的待办内容组件

```javascript
Vue.component('todo-title',{
        props:['title'],
        template:'<div>{{title}}</div>'
    });
   
12345
//这里的index，就是数组的下标，使用for循环遍历的时候，可以循环出来！
    Vue.component("todo-items",{
        props:["item","index"],
        template:"<li>{{index+1}},{{item}}</li>"
    });
12345
```

2.3 实例化Vue并初始化数据

```javascript
 var vm = new Vue({
        el:"#vue",
        data:{
            todoItems:['test1','test2','test3']
        }
    });
123456
```

2.4 将这些值，通过插槽插入

```html
<div id="vue">
    <todo>
        <todo-title slot="todo-title" title="秦老师系列课程"></todo-title>
        <!--<todo-items slot="todo-items" v-for="{item,index} in todoItems" v-bind:item="item"></todo-items>-->
        <!--如下为简写-->
        <todo-items slot="todo-items" v-for="(item,index) in todoItems" 
                    :item="item" :index="index" ></todo-items>
    </todo>
</div>
12345678
```

说明：我们的todo-title和todo-items组件分别被分发到了todo组件的todo-title和todo-items插槽中

### 3、完整代码如下：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层，模板-->
<div id="vue">
    <todo>
        <todo-title slot="todo-title" :title="title"></todo-title>
        <!--如下为简写-->
        <todo-items slot="todo-items" v-for="(item,index) in todoItems" v-bind:item="item" v-bind:index="index"  ></todo-items>

    </todo>
</div>

<!--1.导入Vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.5.21/dist/vue.min.js"></script>
<script type="text/javascript">
    Vue.component('todo',{
        template:'<div>\
                <slot name="todo-title"></slot>\
                <ul>\
                    <slot name="todo-items"></slot>\
                </ul>\
            </div>'
    });
    Vue.component('todo-title',{
        props:['title'],
        template:'<div>{{title}}</div>'
    });
    //这里的index，就是数组的下标，使用for循环遍历的时候，可以循环出来！
    Vue.component("todo-items",{
        props:['item','index'],
        template:"<li>{{index}} ----- {{item}}</li>"
    });

    var vm = new Vue({
        el:"#vue",
        data:{
            title:"秦老师系列课程",
            todoItems:['test1','test2','test3']
        }
    });
</script>
</body>
</html>
```



