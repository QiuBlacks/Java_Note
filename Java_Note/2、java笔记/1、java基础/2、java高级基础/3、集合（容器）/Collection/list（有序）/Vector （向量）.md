# **Vector （向量）**

# **一、基本介绍**

## **1、特点**

**可实现自动增长的对象数组**

**可以往其中随意插入不同类的对象**

## **2、构造方法**

```
//创建初始容量为10的空的Vector；若再增加元素，则成倍的增长；
public vector()           	
 //initialcapacity设定向量对象的容量（即向量对象可存储数据的大小），当真正存放的数据个数超过容量	时。系统会扩充向量对象存储容量。		
public vector(int initialcapacity)      
 //参数capacityincrement给定了每次扩充的扩充值。当capacityincrement为0的时候，则没次扩充一倍，利用这个功能可以优化存储。
public vector(int initialcapacity,int capacityIncrement) 
```



## **3、初始化**

vector< 类型>   vector(n, a ); 定义了 vector它包含n个类型的元素 每个元素都被初始化为-1

## **4、常用方法**

**1）public final synchronized void adddElement(Object obj)**

**注意：**插入的应是对象而不是数值，所以插入数值时要注意将数组转换成相应的对象

```
Vector v1 = new Vector();  

Integer integer1 = new Integer(1);  

v1.addElement(integer1); 
```

对于预先不知或者不愿预先定义数组大小，并且需要频繁地进行查找，插入，删除工作的情况。可以考虑使用向量类。