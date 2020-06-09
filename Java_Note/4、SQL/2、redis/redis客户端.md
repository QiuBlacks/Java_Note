## redis客户端



## 一、redis的py客户端

### 1、安装redis-py

```bash
wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tgz
mkdir -p /usr/local/python3
cd /usr/local/python3
tar -xvf Python-3.6.3.tgz 
cd Python-3.6.3/
 ./configure --prefix=/usr/local/python3Dir
 make
 make install
cd /usr/bin
mv python python.bak
ln -s /usr/local/python3/bin/python3 /usr/bin/python

vi /usr/bin/yum
把文件开头第一行的 #!/usr/bin/python  改成  #!/usr/bin/python2.7
sudo vim /etc/profile
末尾添加：
export PATH=$PATH:/usr/local/python3/bin
```



```
pip3 install redis
```

参考：

​		[升级py](https://blog.csdn.net/weixin_41798704/article/details/88238222)

​		https://www.cnblogs.com/Jimc/p/10218387.html





## 二、redis的C客户端

### 1、安装hiredis

```java
wget https://github.com/redis/hiredis/archive/v0.14.0.tar.gz #获取压缩包
cd hiredis-0.14.0/
make -j
make install
cp libhiredis.so  /usr/lib 
/sbin/ldconfig
```

### 2、编译

```bash
 gcc -o  test  test.c  -l  hiredis
   ./text
```

