[TOC]



# JVM（Java虚拟机）

---
## 一、基本介绍
### 1、JVM介绍

Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。

JVM是Java Virtual Machine（Java虚拟机）的缩写，是一种用于计算设备的规范，它是一个虚构出来的计算机，是通过在实际的计算机上仿真模拟各种计算机功能来实现的。<br>
 &emsp;&emsp;JVM屏蔽了与具体操作系统平台相关的信息，JVM在执行字节码时，实际上最终还是把字节码解释成具体平台上的机器指令执行。
		通过JVM，Java实现了**平台无关性**，Java语言在不同平台运行时不需要重新编译，只需要在该平台上部署JVM就可以了。因而能实现一次编译多处运行。

Java虚拟机包括一套字节码指令集、一组寄存器、一个栈、一个垃圾回收堆和一个存储方法域。

### 2、HotSpot 、JVM、openJdk关系
HotSpot 是JVM概念的一个实现。它最初由Sun开发，现在由Oracle拥有。 JVM规范还有其他实现，例如 JRockit ， IBM J9 等等。<br>
&emsp;&emsp;jvm是规范，而HotSpot是实现方式，并且在openJdk的项目上开发的。


### 3. JRE和JDK
JRE：Java Runtime Environment，也就是JVM的运行平台，联系平时用的虚拟机，大概可以理解成：JRE=虚拟机平台+虚拟机本体(JVM)。类似于你电脑上的VMWare+适用于VMWare的Ubuntu虚拟机。这样我们也就明白了JVM到底是个什么。<br>
&emsp;&emsp;JDK：Java Develop Kit，Java的开发工具包，JDK本体也是Java程序，因此运行依赖于JRE,由于需要保持JDK的独立性与完整性，JDK的安装目录下通常也附有JRE。目前Oracle提供的Windows下的JDK安装工具会同时安装一个正常的JRE和隶属于JDK目录下的JRE

### 4、运行过程：
-  Java源文件—->编译器—->字节码文件<br>
-  字节码文件—->JVM—->机器码<br>

我们都知道Java源文件，通过编译器，能够生产相应的.Class文件，也就是字节码文件，而字节码文件又通过Java虚拟机中的解释器，编译成特定机器上的机器码 

 每一种平台的解释器是不同的，但是实现的虚拟机是相同的，这也就是Java为什么能够跨平台的原因了 ，当一个程序从开始运行，这时虚拟机就开始实例化了，多个程序启动就会存在多个虚拟机实例。程序退出或者关闭，则虚拟机实例消亡，多个虚拟机实例之间数据不能共享。<br>

<img src="E:\black user\Java\有道云截图\image-20200611210243710.png" alt="image-20200611210243710"  />



## 二、JVM体系结构
### 1、总图解：

​		类加载器子系统、运行时数据区、执行引擎 、本地方法接口

![image](https://www.pdai.tech/_images/jvm/java-jvm-overview.png)



<img src="E:/black user/Java/有道云截图/image-20200612104642003.png" alt="image-20200612104642003" style="zoom:80%;" />

### 2、组成详解

#### 1）Class Loader类加载器

负责加载 .class文件，class文件在文件开头有特定的文件标示，并且ClassLoader负责class文件的加载等，至于它是否可以运行，则由Execution Engine决定。

#### 2 ）Native Interface本地接口

本地接口的作用是融合不同的编程语言为Java所用，它的初衷是融合C/C++程序，Java诞生的时候C/C++横行的时候，要想立足，必须有调用C/C++程序，于是就在内存中专门开辟了一块区域处理标记为native的代码，它的具体作法是Native Method Stack中登记native方法，在Execution Engine执行时加载native libraies。

目前该方法使用的越来越少了，除非是与硬件有关的应用，比如通过Java程序驱动打印机，或者Java系统管理生产设备，在企业级应用中已经比较少见， 因为现在的异构领域间的通信很发达，比如可以使用Socket通信，也可以使用Web Service等。

#### 3 ）Execution Engine 执行引擎

执行包在装载类的方法中的指令，也就是方法。

#### 4 ）Runtime data area 运行数据区

虚拟机内存或者Jvm内存，冲整个计算机内存中开辟一块内存存储Jvm需要用到的对象，变量等，运行区数据有分很多小区，分别为：方法区，虚拟机栈，本地方法栈，堆，程序计数器。




## 三、运行时数据区（内存）
#### jdk7<br>

![image-20200612104820554](E:/black user/Java/有道云截图/image-20200612104820554.png)



#### JDK8<br>

<img src="E:\black user\Java\有道云截图\20190801003717155 (2).png" alt="20190801003717155 (2)" style="zoom:80%;" />



JVM主要包括：程序计数器(Program Counter)，Java堆(Heap)，Java虚拟机栈(Stack)，本地方法栈(Native Stack)，方法区(Method Area)


### 1、程序计数器：(Program Counter)
&emsp;&emsp;当前线程所执行的字节码的行号指示器，用于记录正在执行的虚拟机字节指令地址，==线程私有==，用于指示，跳转下一条需要执行的命令。

### 2、Java虚拟栈：(Stack)

&emsp;&emsp;存放基本数据类型、对象的引用、方法出口等，==线程私有==。

### 3、Native本地方法栈：
&emsp;&emsp;和虚拟栈相似，只不过它服务于Native方法==，线程私有==。

### 4、Java堆：(Heap)
&emsp;&emsp;java内存最大的一块，所有对象实例、数组都存放在java堆，GC回收的地方，==线程共享==。

### 5、方法区：(Method Area)
所有定义的方法的信息都保存在该区域，此区域属于共享区间。<br>
&emsp;&emsp;存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码数据等。回收目标主要是常量池的回收和类型的卸载，各==线程共享==

















