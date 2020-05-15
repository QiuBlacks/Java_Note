# **model**

**一、基本介绍**

**1、概念**

控制器是通过Model对象把数据传到view层的.

**2、作用**

主要是保存数据，可以借助它们将数据带到前端

**二、使用方法**

```
  @RequestMapping("/testString")
    public String testString(Model model){
        System.out.println("testString方法执行了...");
        // 模拟从数据库中查询出User对象
        User user = new User();
        user.setUsername("美美");
        user.setPassword("123");
        user.setAge(30);
        // model对象
        model.addAttribute("user",user);
        return "success";
    }
```

**model会将数据****存放在requestScope中，然后我们可以在表现层调用**

```
${user.Username}
${requestScope}
```

**三、****ModelMap以及ModelAndView**