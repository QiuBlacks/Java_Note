## 剑指Offer53 - II. 0～n-1 中缺失的数字（二分法）

## 一、题目描述

一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0 ～ n - 1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

示例：

```
输入: [0,1,2,3,4,5,6,7,9]
输出: 8
```



## 二、解题方法

### 1、自制二分法

```Java
class Solution {
    public int missingNumber(int[] nums) {
	//考虑太多
   //     if(nums[len-1]==len&&nums[0]!=0) return 0;
   //     if(nums[len-1]==len-1) return len;
        
        int left=0;   //出错点：int left=nums[0];
        int right=nums.length-1;
        int mid=0;
        while(left<right){
             mid=(right+left)/2;
            int count=mid;
            if(mid!=nums[mid]){
                right=mid-1;
            }else left=mid+1;
        }
        return mid;
    }
}
```

### 2、简洁版

```Java
class Solution {
    public int missingNumber(int[] nums) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] == m) i = m + 1;
            else j = m - 1;
        }
        return i;
    }
}
```

### 3、暴力法

```Java
  public int missingNumber(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (i != nums[i])
                return i;
        }
        return nums.length;
    }
```

