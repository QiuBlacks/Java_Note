# 剑指Offer16. 数值的整数次方（快速幂）

## 一、题目介绍

实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题

即求base的exponent次方

**说明:**

- -100.0 < *x* < 100.0
- n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 



## 二、实现方法

### 1、快速幂（非递归二分法）

参考：https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/solution/mian-shi-ti-16-shu-zhi-de-zheng-shu-ci-fang-kuai-s/

![image-20200513223457004](E:\black user\Java\有道云截图\image-20200513223457004.png)

```java
class Solution {
    public double myPow(double x, int n) {
        if(x == 0) return 0;
        long b = n;
        double res = 1.0;
        if(b < 0) {
            x = 1 / x;
            b = -b;
        }
        while(b > 0) {
            if((b & 1) == 1) res *= x;
            x *= x;
            b >>= 1;
        }
        return res;
    }
}
```



## 2、递归二分法

```
class Solution {
    public double myPow(double x, int n) {
        if (n == 0)
		return 1.0;
	if (n == 1)
		return x;
	if (n ==-1) return 1/x;
	double res = myPow(x * x, n >>1);
	return res * ((n & 1) == 0 ? 1 : x);
    }
}
```



## 3、分治算法

参考：https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/solution/di-gui-xie-fa-fen-zhi-si-xiang-yu-fei-di-gui-xie-f/

```
public class Solution {

    public double myPow(double x, int n) {
        // 特判，也可以认为是递归终止条件
        long N = n;
        if (N < 0) {
            return 1 / myPow(x, -N);
        }
        return myPow(x, N);
    }

    private double myPow(double x, long n) {
        if (n == 0) {
            return 1;
        }

        if (x == 1) {
            return 1;
        }

        // 根据指数是奇数还是偶数进行分类讨论
        // 使用位运算的 与 运算符代替了求余数运算

        if ((n % 2) == 0) {
            // 分治思想：分
            double square = myPow(x, n / 2);
            // 分治思想：合，下面同理
            return square * square;
        } else {
            // 是奇数的时候
            double square = myPow(x, (n - 1) / 2);
            return square * square * x;
        }
    }

}
```

