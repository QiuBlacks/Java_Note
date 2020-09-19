



# 一、四大@Mapping

##  @GetMapping : select

 @GetMapping == 》@RequestMapping(method = RequestMethod.GET)

@GetMapping("/get/{id}")

get请求：可以在url后面输入参数操作

1. 直接在浏览器地址栏输入某个地址

2. 表单默认的提交方式

   

## @PostMapping : add

@PostMapping ==》@RequestMapping(method = RequestMethod.POST)

@PostMapping("/get/{id}")

post请求：

- 设置表单method = “post”

- ajax type: ‘post’,

  

## @PutMapping：update





## @DeleteMapping : delete





## @PatchMapping







# 二、get、put

### 1、get请求特点：

 请求参数会添加到请求资源路径的后面，只能添加少量参数（因为请求行只有一行，大约只能存放2K左右的数据）

请求参数会显示在浏览器地址栏，路由器会记录请求地址 （极为的不安全）

如果传输中文，必定会乱码（原因：get请求默认编码格式为：IIO-8859-1,后台编码格式一般为：GBK或者UTF-8）

### 2、post请求的特点：

-  请求参数添加到实体内容里面，可以添加大量的参数（也解释了为什么浏览器地址栏不能发送post请求，在地址栏里我们只能填写URL，并不能进入到Http包的实体当中）
- 相对安全，但是，post请求不会对请求参数进行加密处理（可以使用https协议来保证数据安全）

