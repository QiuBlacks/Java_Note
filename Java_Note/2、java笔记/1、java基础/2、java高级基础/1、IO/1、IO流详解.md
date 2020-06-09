# IO流详解
[TOC]



## 一、分类
### 1、按流的方向分类：
-   输入流：数据流向是数据源到程序(以InputStream、Reader结尾的流)。
-   输出流：数据流向是程序到目的地(以OutPutStream、Writer结尾的流)。

### 2、按处理的数据单元分类：
- 字节流：以字节为单位获取数据，命名上以Stream结尾的流一般是字节流，如FileInputStream、FileOutputStream。
-   字符流：以字符为单位获取数据，命名上以Reader/Writer结尾的流一般是字符流，如FileReader、FileWriter

### 3、按处理对象不同分类：
-  节点流：可以直接从数据源或目的地读写数据，如FileInputStream、FileReader、DataInputStream等。
-   处理流：不直接连接到数据源或目的地，是”处理流的流”。通过对其他流的处理提高程序的性能，如BufferedInputStream、BufferedReader等。处理流也叫包装流。<br>

节点流处于IO操作的第一线，所有操作必须通过它们进行;处理流可以对节点流进行包装，提高性能或提高程序的灵活性

## 二、主要知识点
### 从数据操作上，I/O 大概可以分成以下几类：

- 磁盘操作：File
- 字节操作：InputStream 和 OutputStream
- 字符操作：Reader 和 Writer
- 对象操作：Serializable
- 网络操作：Socket
- 新的输入/输出：NIO
![image](https://www.pdai.tech/_images/io/java-io-category-3.png)


## 三、字符、字节流
### 1、图解
![image-20200608194143354](E:\black user\Java\有道云截图\image-20200608194143354.png)

### 2、 四大IO抽象类


#### 1）InputStream
  此抽象类是表示字节输入流的所有类的父类。InputSteam是一个==抽象类==，它不可以实例化。<br>		 数据的读取需要由它的子类来实现。根据节点的不同，它派生了不同的节点流子类 。<br>
 		 继承自InputSteam的流都是用于向程序中输入数据，且数据的单位为字节(8 bit)。<br>
  常用方法：<br>

-   int read()：读取一个字节的数据，并将字节的值作为int类型返回(0-255之间的一个值)。如果未读出字节则返回-1(返回值为-1表示读取结束)。<br>
-   void close()：关闭输入流对象，释放相关系统资源。

#### 2） OutputStream
  此抽象类是表示字节输出流的所有类的父类。输出流接收输出字节并将这些字节发送到某个目的地。<br>
#####   常用方法：<br>

```
void write(int n)：向目的地中写入一个字节。
 void close()：关闭输出流对象，释放相关系统资源。
```

#### 3）Reader
  Reader用于读取的字符流抽象类，数据单位为字符。<br>

```
int read(): 读取一个字符的数据，并将字符的值作为int类型返回(0-65535之间的一个值，即Unicode值)。如果未读出字符则返回-1(返回值为-1表示读取结束)
void close() ： 关闭流对象，释放相关系统资源。
```

#### 4）Writer
  Writer用于写入的字符流抽象类，数据单位为字符。

```
void write(int n)： 向输出流中写入一个字符。
void close() ： 关闭输出流对象，释放相关系统资源。
```


## 四、标准步骤



1、创建源
2、选择流
3、操作
4、释放资源