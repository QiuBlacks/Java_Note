# 剑指Offer62、圆圈中最后剩下的数字

## 一、题目描述

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始**每次删除第3个数字**，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

 

示例 1：

```
输入: n = 5, m = 3
输出: 3
```



## 二、解题方法

### 1、数学

解题思路：

最终剩下一个人时的安全位置肯定为0，反推安全位置在人数为n时的编号
人数为1： 0
人数为2： (0+m) % 2
人数为3： ((0+m) % 2 + m) % 3
人数为4： (((0+m) % 2 + m) % 3 + m) % 4   

迭代推理到n就可以得出答案

```
class Solution {
    public int lastRemaining(int n, int m) {
        int ans = 0;
        // 最后一轮剩下2个人，所以从2开始反推
        for (int i = 2; i <= n; i++) {
            ans = (ans + m) % i;
        }
        return ans;
    }
}
```





### 2、自制ArrayList

```
class Solution {
    public int lastRemaining(int n, int m) {
        int index = 0;
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            list.add(i);
        }

        while (list.size() > 1) {
            index = (index + m - 1) % list.size();
            list.remove(index);
        }
        return list.get(0);
    }
}
```

执行用时：1092 ms, 在所有 Java 提交中击败了11.49%的用户

内存消耗：40.4 MB, 在所有 Java 提交中击败了46.33%的用户



## 参考

[约瑟夫环——公式法（递推公式）](https://blog.csdn.net/u011500062/article/details/72855826)