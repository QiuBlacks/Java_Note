# **$PATH**

## **一、问题**：

当你在linux中的任何一个目录下输入pwd命令，都会显示当前目录的位置，pwd可是一条命令啊，这条命令是存放在某个目录下，为什么你在任何地方都可以使用pwd命令的原因？

**解：**

[root@localhost human]# whereis pwd;

pwd: /bin/pwd /usr/include/pwd.h /usr/share/man/mann/pwd.n.gz /usr/share/man/man1/pwd.1.gz

安装linux系统时，已经默认在PATH中写进了/bin/pwd，当你执行pwd时，计算机首先去/bin中去查找，发现存在pwd命令，可以使用。



## **二、**假如我现在将/bin/ls命令剪切到/lives下，你是否还可以在任何地方使用ls呢？

解：可以，但不能继续直接使用pwd命令，虽然PATH中存在/bin目录，但是/bin目录下此时并没有pwd命令。

### **解决方法：**

#### 方法一：使用绝对路径

[root@localhost lives]# /lives/ls

human ls



#### 方法二：使用相对路径

[root@localhost lives]# ./ls

human ls

问题：为什么刚才在/lives中直接使用ls不可以，而此处使用./ls就可以呢？这个问题你知道麻烦你告诉我，我不知道。



#### 方法三：自己设置将/lives添加到PATH中

[root@localhost lives]# PATH="$PATH":/lives;

[root@localhost lives]# ls

human ls