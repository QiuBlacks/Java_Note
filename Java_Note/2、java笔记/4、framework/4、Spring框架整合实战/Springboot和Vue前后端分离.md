# Springboot和Vue前后端分离

前端和后端看成两个不同的应用程序分离实现，前端通过 Ajax 请求来访问后端的数据接⼝，将 Model 展示到 View 中即可。  

解决跨域问题：前端通过axios访问后端的接口获取数据，而后端开启跨域允许前端访问数据



完整代码：F:\java\练习项目\Springboot案例\前后端分离案例Book



# 实验过程

## 一、前端8080端口

### 1、项目准备

新建一个vue-cli项目，安装vue-router、axios

```
//运行
cd myvue
npm install
npm run dev
//安装
npm install axios
```



### 2、配置index.js

导入Book.vue

```js
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Book from '../view/Book'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/book',  //通过这个路径访问Book.vue
      name:"查询图书",
      component: Book
    }
  ]
})
```

### 3、配置Book.vue

```vue
<template>
  <div>
    <table>
      <tr>
        <td>编号</td>
        <td>图书</td>
        <td>作者</td>
      </tr>
      <tr v-for="item in books">
        <td>{{item.id}}</td>
        <td>{{item.name}}</td>
        <td>{{item.author}}</td>
      </tr>
    </table>
  </div>
</template>

<script>
  export default {
    data(){
      return{
        books: [
          {
            id: 1,
            name: '解忧杂货店',
            author: '东野圭吾'
          },{
            id: 2,
            name: '追风筝的人',
            author: '卡勒德·胡赛尼'
          },{
            id: 3,
            name: '人间失格',
            author: '太宰治'
          }
        ]
      }
    },
    //利用$axios向后端访问
    created() {
      const _this = this
      this.$axios.get('http://localhost:8181/book/findAll' ).then((resp) => {
        this.books = resp.data
        //console.log(resp)
      })
    }
  }
</script>

```

### 4、配置main.js

导入router、axios

```js
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue'
import App from './App'
import router from './router'
import axios from 'axios';
Vue.prototype.$axios = axios;

Vue.config.productionTip = false

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```



## 二、后端8081端口

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201210225148.png" alt="image-20201210225148832" style="zoom:80%;" />

### 1、创建实体类Book

```
@Entity
@Data
public class Book {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer id;
    private String name;
    private String author;
}
```

### 2、继承JPA接口BookRepository

```
public interface BookRepository extends JpaRepository<Book,Integer> {

}
```

### 3、 编写yml配置类

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/library?useUnicode=true&characterEncoding=UTF-8&serverTimezone=Asia/Shanghai
    username: root
    password: qrj15521026074
    driver-class-name: com.mysql.cj.jdbc.Driver
  jpa:
    show-sql: true
    properties:
      hibernate:
      format_sql: true
server:
  port: 8181
```

可以先进行测试

```
@Ignore
@SpringBootTest
class BookRepositoryTest {
    @Autowired
    BookRepository bookRepository;
    @Test
    void findAll(){
        System.out.println(bookRepository.findAll());
    }
}
```



### 4、编写Controller类

```
@RestController
@RequestMapping("/book")
public class BookHandler {
    @Autowired
    private BookRepository bookRepository;

    @GetMapping("/findAll")
    public List<Book> findAll(){
        return bookRepository.findAll();
    }

}
```

至此后端配置基本完毕



## 三、前后端分离对接

### 1、前端访问后端

在book.vue配置axios

```
 //利用$axios向后端访问
    created() {
      const _this = this
      this.$axios.get('http://localhost:8181/book/findAll' ).then((resp) => {
        this.books = resp.data
        //console.log(resp)
      })
    }
```

### 2、后端跨域访问前端

编写跨域配置类

```
@Configuration
public class CrosConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowedMethods("GET", "HEAD", "POST", "PUT", "DELETE", "OPTIONS")
                .allowCredentials(true)
                .maxAge(3600)
                .allowedHeaders("*");
    }
}
```

### 3、项目结果

在前端8080端口访问到后端的数据

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201210224810.png" alt="image-20201210224810494" style="zoom:67%;" />



![image-20201210230237247](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20201210230237.png)



# 实验问题

### 1、跨域问题

在book.vue中直接写axios.get会报错axios is not defined

```
created() {
      const _this = this
      axios.get('http://localhost:8181/book/findAll' ).then((resp) => {
        this.books = resp.data
        //console.log(resp)
      })
    }
```

解决方法

在main.js里写

```
import axios from 'axios';
Vue.prototype.$axios = axios;
```

然后将上述代码改成

```
created() {
      const _this = this
      this.$axios.get('http://localhost:8181/book/findAll' ).then((resp) => {
        this.books = resp.data
        //console.log(resp)
      })
    }
```







## 参考

[B站视频p1](https://www.bilibili.com/video/BV137411B7vB?p=1)