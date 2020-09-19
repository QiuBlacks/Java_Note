# 面试题11. 旋转数组的最小数字

## 一、题目描述

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。

示例 1：

```
输入：[3,4,5,1,2]
输出：1
```





## 二、解题方法

### 1、二分法

#### 1.1 算法思路

参考：https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/solution/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-by-leetcode-s/

#### 1.2  代码

```
class Solution {
    public int minArray(int[] numbers) {
        int i = 0, j = numbers.length - 1;
        while (i < j) {
            int m = (i + j) / 2;	
            if (numbers[m] > numbers[j]) i = m + 1;
            else if (numbers[m] < numbers[j]) j = m;
            else j--;
        }
        return numbers[i];
    }
}
```



### 2、排序法

将数组排好序，再返回

```
class Solution {
    public int minArray(int[] numbers) {
        int len=numbers.length;
        int temp=0;
        for(int i=0;i<len;i++)
            for(int j=i;j<len;j++){
                if(numbers[i]>numbers[j]){
                   temp=numbers[i];
                   numbers[i]=numbers[j];
                   numbers[j]=temp;
                }
        }
        return numbers[0];
    }
}
```



3、

```
class Solution {
    public int minArray(int[] numbers) {
        for(int i=0;i<numbers.length-1;i++){
            if (numbers[i]>numbers[i+1]) return numbers[i+1];
        }
        return numbers[0];
    }
}
```

