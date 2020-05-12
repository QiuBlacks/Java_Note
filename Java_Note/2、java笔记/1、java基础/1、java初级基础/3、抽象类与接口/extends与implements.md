# extends与implements

## 一、区别

### **1、是否重写**

extends 是继承某个类, 继承之后可以使用父类的方法, 也**可以重写父类的方法**; 

implements 是实现多个接口, 接口的方法一般为空的, 必须**重写该接口的所有方法**才能使用 

### **2、实现的数量**	

extends只能继承**一个**父类，只要那个类不是声明为final或者那个类定义为abstract的就能继承（JAVA中**不支持多重继承**）

implements可以实现**多个接口**，用逗号分开就行了

class A extends B implements C,D,E

### **3、属性比较**

- 接口的字段只能是 static 和 final 类型的，而抽象类的字段没有这种限制。
- 接口的成员只能是 public 的，而抽象类的成员可以有多种访问权限。

