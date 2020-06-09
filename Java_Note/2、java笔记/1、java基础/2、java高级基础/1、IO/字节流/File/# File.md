# **File**

## **一、基本介绍**

### **1、基本概念**

File对象既可以表示文件，也可以表示目录。特别要注意的是，构造一个File对象，即使传入的文件或目录不存在，代码也不会出错，因为构造一个File对象，并不会导致任何磁盘操作。只有当我们调用File对象的某些方法的时候，才真正进行磁盘操作。

### **2、创建File对象**

```Java
String path="E:/black user/Pictures/Camera Roll/1.jpg"; 
//第一种  
File(String pathname) File src=new File(path);
//第二种 
File(File Parent, String child) File src=new File("E:/black user/Pictures/Camera Roll","1.jpg"); 
//第三种  
File(String Parent, String child) File src=new File(new File("E:/black user/Pictures Roll"),"Camera/1.jpg"); 文件不存在时自动构建
```

二、