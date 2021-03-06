## 剑指Offer39. 数组中出现次数超过一半的数字

## 一、题目描述

数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。



## 二、解题方法

### 1、摩尔投票法（双百）

参考：[力扣大佬](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/solution/mian-shi-ti-39-shu-zu-zhong-chu-xian-ci-shu-chao-3/)

```java
class Solution {
    public int majorityElement(int[] nums) {
        int x = 0, votes = 0;
        for(int num : nums){
            if(votes == 0) x = num;
            votes += num == x ? 1 : -1;
        }
        return x;
    }
}
```



### 2、自制hashmap

```java
class Solution {
    public int majorityElement(int[] nums) {
        HashMap<Integer,Integer> map = new HashMap<>();
        int index=-1,max=0;
        for(int i=0;i<nums.length;i++){
            if(map.containsKey(nums[i])){
                 map.put(nums[i],map.get(nums[i])+1);
            }else{
                map.put(nums[i],1);
            }
           
            if(max<map.get(nums[i])){
                max=Math.max(map.get(nums[i]),max);
                index=nums[i];
            }
        }

        return max>nums.length/2?index:0;
    }
}
```

执行用时 :28 ms, 在所有 Java 提交中击败了7.02%的用户

内存消耗 :43.6 MB, 在所有 Java 提交中击败了100.00%的用户