## pipeline(管道)





```python
#!/usr/bin/python3
# -*- coding: utf-8 -*- 
#导包
import redis
import time 

r = redis.Redis(host='127.0.0.1', port=6379,db=0)
#非pipeline代码：
def without_pipeline():
    r=redis.Redis()
    sum1=0
#没有使用pipeline的情况下循环添加
    for i in range(20000):
#i为key值，value为(i%10)/10
        r.hset("dic_name",i,(i%10)/10)
        sum1 += float(r.hget('dic_name', i).decode('utf-8'))
    print(sum1)
    return


 #使用pipeline的情况下循环添加
def  with_pipeline():
    r=redis.Redis()
    pipeline=r.pipeline()
    sum2=0
    j=0
    for i in range(20000):
        pipeline.hset("dic_name",i,(i%10)/10)
        pipeline.hget("dic_name",i)
    result=pipeline.execute()
 
    for i in range(1,40001,2):
        sum2+=float(result[i].decode('utf-8'))
    print(sum2)
    return 

#计时
def bench(desc):
    start=time.clock()
    desc()
    stop=time.clock()
    diff=stop-start
    print("%s  has  taken  %s  seconds"  %(desc.__name__,str(diff)))


if  __name__ == '__main__':
    bench(without_pipeline)
    bench(with_pipeline)
```

execute()来获取hget的值，但显示结果如下：每个vaule值中间隔个0

```java
0
b '0.0'
0
b '0.1'
0
b '0.2'
0
b '0.3'
...
...
```