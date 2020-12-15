# 剑指Offer56 -I、数组中数字出现的次数

## 一、题目描述

一个整型数组 `nums` 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。



```
输入: [1,2,1,3,2,5]
输出: [3,5]或者[5,3]
```





## 二、解题方法

## 1、位运算

主要思想：

通过异或运算，那些出现两次的数字都会变成0，最后只剩下两个出现一次的数字

但我们只能得到两个数字的异或结果，于是就想办法把他们分开到两组进行计算异或

通过mask得到两个数不同的那个位置，即一个0，一个1

就可以通过num & mask将两个数分开，得到最后的结果

出现两次的数字通过num & mask肯定还是分在一个组里的

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        //用于将所有的数异或起来
        int k = 0;
        
        for(int num: nums) {
            k ^= num;
        }
        
        //获得k中最低位的1
        int mask = 1;

        //mask = k & (-k) 这种方法也可以得到mask，具体原因百度 哈哈哈哈哈
        while((k & mask) == 0) {
            mask <<= 1;
        }
        
        int a = 0;
        int b = 0;
        
        for(int num: nums) {
            //分开两个数
            if((num & mask) == 0) {
                a ^= num;
            } else {
                b ^= num;
            }
        }
        
        return new int[]{a, b};
    }
}
```

执行用时：2 ms, 在所有 Java 提交中击败了97.13%的用户

内存消耗：40.7 MB, 在所有 Java 提交中击败了14.12%的用户





### 2、哈希表

```
class Solution {
    public int[] singleNumbers(int[] nums) {
        Map<Integer, Integer> hashmap = new HashMap();
        for (int n : nums)
            hashmap.put(n, hashmap.getOrDefault(n, 0) + 1);

        int[] output = new int[2];
        int idx = 0;
        for (Map.Entry<Integer, Integer> item : hashmap.entrySet()){
            if (item.getValue() == 1)
                output[idx++] = item.getKey();
        }
        return output;
    }
}
```



