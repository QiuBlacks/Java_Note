# javaBean
----------------------------------------------------------------------


# 一、JavaBean简介
&emsp; &emsp;JavaBean是使用Java语言开发的一个可重用的组件，在JSP的开发中可以使用JavaBean减少重复代码，使整个JSP代码的开发更简洁。

## 1、JSP搭配JavaBean来使用，有以下的优点：
&emsp; &emsp;可将HTML和Java代码分离，这主要是为了日后维护的方便。如果把所有的程序代码（HTML和Java）写到JSP页面中，会使整个程序代码又多又复杂，造成日后维护上的困难。<br>
&emsp; &emsp;可利用JavaBean的优点。将日常用到的程序写成JavaBean组件，当在JSP要使用时，只要调用JavaBean组件来执行用户所要的功能，不用再重复写相同的程序，这样以来也可以节省开发所需的时间。

## 2、JSP中JavaBean的要求
(1)所有的类必须放在一个包中，在WEB中没有包的是不存在的；<br>
(2)所有的类必须声明为public class，这样才能够被外部所访问；<br>
(3)类中所有的属性都必须封装，即：使用private声明；<br>
(4)封装的属性如果需要被外部所操作，则必须编写对应的setter、getter方法；<br>
(5)一个JavaBean中至少存在一个无参构造方法，此为JSP中的标签所使用。  

## 3、实例

```
public class SimpleBean{  
    private String name;  
 
    public void setName(String name){  
        this.name = name;  
    }  
    public String getName(){  
        return this.name;  
    }  
}
```



# 二、JavaBean的作用
&emsp; &emsp;JavaBean主要用来传递数据，即把一组数据组合成一个JavaBean便于传输。<br>
&emsp; &emsp;此外，JavaBean可以方便地被IDE工具分析，生成读写属性的代码，主要用在图形界面的可视化设计中。
