# 剑指Offer21. 调整数组顺序使奇数位于偶数前面（双指针）

## 一、题目描述

输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分



## 二、解题方法

### 1、双指针

```java
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0, j = nums.length - 1, tmp;
        while(i < j) {
            while(i < j && (nums[i] & 1) == 1) i++;
            while(i < j && (nums[j] & 1) == 0) j--;
            if(i<j){
            tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
            }
        }
        return nums;
    }
}
```

### 2、自制+位运算（逼格更高）

```java
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0, j = nums.length - 1, tmp;
        while(i < j) {
            while(i < j && (nums[i] & 1) == 1) i++;
            while(i < j && (nums[j] & 1) == 0) j--;
            if(i<j){
                nums[i]=nums[i]^nums[j];
                nums[j]=nums[i]^nums[j];
                nums[i]=nums[i]^nums[j];
           		 }
        }
        return nums;
    }
}
```