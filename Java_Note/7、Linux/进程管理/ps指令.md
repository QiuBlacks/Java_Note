# **显示系统执行的进程** 

 

# **ps指令详解**

## **一、指令参数：**

查看进行使用的指令是 ps ,一般来说使用的参数是 ps -aux

![image-20200513233550194](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910183314.png)



## 二、指令：

ps –aux|grep xxx ，比如我看看有没有 sshd服务

## **三、指令结果说明**

 

```
•System V展示风格

•USER：用户名称
 
•PID：进程号

PPID：父进程 ID

•%CPU：进程占用 CPU的百分比

•%MEM：进程占用物理内存的百分比

•VSZ：进程占用的虚拟内存大小（单位：KB）

•RSS：进程占用的物理内存大小（单位：KB）

•TT：终端名称,缩写 .

•STAT：进程状态，其中 S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等

•STARTED：进程的启动时间

•TIME：CPU时间，即进程使用 CPU的总时间

•COMMAND：启动进程所用的命令和参数，如果过长会被截断显示
```



## **四、显示结果**

![image-20200513233647447](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910183315.png)