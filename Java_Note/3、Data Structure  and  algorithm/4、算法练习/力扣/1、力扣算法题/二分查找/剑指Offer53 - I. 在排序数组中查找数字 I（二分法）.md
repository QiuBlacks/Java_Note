## 剑指Offer53 - I. 在排序数组中查找数字 I（二分法）

## 一、题目介绍

统计一个数字在排序数组中出现的次数

示例：

```
输入: nums = [5,7,7,8,8,10], target = 8
输出: 2
```

##  二、解题思路

### 1、二分法变形

#### 1）算法思路

使用二分法分别找到 左边界 left 和 右边界 right ，易得数字 target 的数量为 right - left - 1

即找到<=target的位置和比target小一位的位置

#### 2）代码实现

```java
class Solution {
    public int search(int[] nums, int target) {
        // 搜索右边界 right
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            //找到target的右位置
            if(nums[m] <= target) i = m + 1;
            else j = m - 1;
        }
        int right = i;
        // 若数组中无 target ，则提前返回
        if(j >= 0 && nums[j] != target) return 0;
        // 搜索左边界 right
        i = 0; j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            //找到比target小一个的数的位置
            if(nums[m] < target) i = m + 1;
            else j = m - 1;
        }
        int left = j;
        return right - left - 1;
    }
}
```

升级版：

```java
class Solution {
    public int search(int[] nums, int target) {
        return helper(nums, target) - helper(nums, target - 1);
    }
    int helper(int[] nums, int tar) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] <= tar) i = m + 1;
            else j = m - 1;
        }
        return i;
    }
}
```

