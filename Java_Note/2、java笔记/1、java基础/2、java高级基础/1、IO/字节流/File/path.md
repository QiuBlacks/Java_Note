## **path**

## **一、path的写法**



```
//1、建议使用：
String path2="E:/black user/Pictures/Camera Roll/1.jpg";  
//2、常量拼接 
String path3="E:"+ File.separator+"balck user"+File.separator+"Pictures"+
				File.separator+"Camera Roll"+File.separator+"1.jpg"; 
```



## 二、相对路径和绝对路径

**1、绝对路径=**相对路径+当前目录

```
// 存在盘符  S
tring path="E:/black user/Pictures/Camera Roll/1.jpg";
```

**2、相对路径**

```
//打印当前目录路径  
System.out.println(System.getProperty("user.dir")); 
path="1.jpg"

 .\\  表示当前目录    
 ..\\   表示上级目录
```

