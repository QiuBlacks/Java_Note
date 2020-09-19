# 1、两数之和（HashMap）

## 一、题目描述

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍

例:

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```



## 二、解题方法

### 1、HashMap

```java
class Solution {
   public int[] twoSum(int[] nums, int target){
        int[] temp = new int[2];
        Map<Integer,Integer> map = new HashMap<>();
        //遍历查找
        for(int i = 0; i < nums.length; i++){
            int a = nums[i];
            //去重
            if(i > 0 && nums[i] == nums[i - 1] && 2*nums[i]!=target) continue;
            if(map.containsKey(target - a)){
                temp[0] = map.get(target - a);
                temp[1] = i;
                return temp;
            }else {//如果找不到则存进去
                map.put(nums[i], i);
            }
        }
        return null;
    }
}
```

### 2、暴力

```
class Solution {
    public int[] twoSum(int[] nums, int target) {
        for(int i=0;i<nums.length;i++){
            for(int j=0;j<nums.length;j++){
                //遇到相同的就跳过
                if(i==j){
                    continue;
                }else {
                    if(nums[i]+nums[j]==target){
                        return new int[]{i,j};
                    }
                }
            }
        }
        return null;
    }
}
```

