

## 1、hashmap扩容的时候为什么是2倍



```
static int indexFor(int h, int length) {  //jdk1.7的源码，jdk1.8没有这个方法，但是实现原理一样的
     return h & (length-1);  //第三步 取模运算
}
```

使用indexFor时需计算 h & (length-1)，如果将容量len为偶数，len-1为奇数， 使得len-1二进制后面都是111结尾，使得h & (length-1)有不同的结果可能性加大，减小碰撞的几率

在 n 为 2次幂的情况下时，(n - 1) & hash ≈ hash % n ,因为2进制的运算速度远远高于取模