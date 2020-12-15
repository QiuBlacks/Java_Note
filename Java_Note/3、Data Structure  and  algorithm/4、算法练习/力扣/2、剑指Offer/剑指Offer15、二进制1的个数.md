# 剑指Offer15、二进制1的个数

## 一、题目描述

请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。

例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

示例 1：

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```





## 二、解题方法

### 1、位运算01

```
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int count=0;
        while(n!=0){
            count += n & 1;
            n >>>= 1;
        }
        return count;
    }
}
```



### 2、位运算02

```
public class Solution {
    public int hammingWeight(int n) {
        int res = 0;
        while(n != 0) {
            res++;
            n &= n - 1;
        }
        return res;
    }
}
```

