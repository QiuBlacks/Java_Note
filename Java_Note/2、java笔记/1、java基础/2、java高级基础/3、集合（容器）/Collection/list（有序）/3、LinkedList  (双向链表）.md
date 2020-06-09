# **LinkedList  (双向链表）**

## **一、基本介绍**

### **1、特点**

- 基于双向链表实现

- 支持队列，克隆和序列化操作

- 与 ArrayList 一样，允许 null 元素的存在，且是不支持多线程
- 非同步，不能随机访问

### **2、属性**

 提供了以下三个成员变量：size，first，last。

### **3、构造方法**

LinkedList() ：构造一个空的列表，没有任何元素。size = 0。first 和 last 都为 null。

LinkedList(Collection<? extends E> c)：构造一个包含指定 Collection 中所有元素的列表，该构造方法首先会调用空的构造方法，然后通过 addAll() 的方式把 Collection 中的所有元素添加进去。

### **4、常用方法**

**add(int index, E element)：在指定的位置插入元素**

get

remove

insert