[TOC]



# TOPK问题

## 一、基本介绍

### 1、问题介绍

找到数组最大或者最小的K个数

### 2、解决方案

#### 1）全局排序

​		 Arrays.sort()、  排序算法

#### 2）局部排序

​		 冒泡排序，找到前k个最小的返回

#### 3）堆排序

#### 4）快排变形





## 二、解决方法

### 1、快排变形（可以进行快排优化）

#### 1）思想

- 若 k = m，我们就找到了最小的 k 个数，就是左侧的数组；
- 若 k<m，则最小的 k个数一定都在左侧数组中，我们只需要对左侧数组递归地 parition 即可；
- 若 k>m，则左侧数组中的 m 个数都属于最小的 k 个数，我们还需要在右侧数组中寻找最小的 k−m 个数，对右侧数组递归地 partition 即可

#### 2）复杂度

空间复杂度 O(1)，不需要额外空间。
		时间复杂度的分析方法和快速排序类似。由于快速选择只需要递归一边的数组，时间复杂度小于快速排序，期望时间复杂度为 O(n)，最坏情况下的时间复杂度为 O(n^2)

#### 3）代码实现

```java
public int[] getLeastNumbers(int[] arr, int k) {
    if (k == 0) {
        return new int[0];
    } else if (arr.length <= k) {
        return arr;
    }
    
    // 原地不断划分数组
    partitionArray(arr, 0, arr.length - 1, k);
    
    // 数组的前 k 个数此时就是最小的 k 个数，将其存入结果
    int[] res = new int[k];
    for (int i = 0; i < k; i++) {
        res[i] = arr[i];
    }
    return res;
}

void partitionArray(int[] arr, int lo, int hi, int k) {
    // 做一次 partition 操作
    int m = partition(arr, lo, hi);
    // 此时数组前 m 个数，就是最小的 m 个数
    if (k == m) {
        // 正好找到最小的 k(m) 个数
        return;
    } else if (k < m) {
        // 最小的 k 个数一定在前 m 个数中，递归划分
        partitionArray(arr, lo, m-1, k);
    } else {
        // 在右侧数组中寻找最小的 k-m 个数，
        //注意：k是即数组的第k个数，原因与m比较，是看m的取值，partition函数return的是数组下标
        partitionArray(arr, m+1, hi, k);
    }
// 否则根据下标j与k的大小关系来决定继续切分左段还是右段。
 //     return j > k? quickSearch(nums, lo, j - 1, k): quickSearch(nums, j + 1, hi, k);
    
}

// partition 函数和快速排序中相同，具体可参考快速排序相关的资料
int partition(int[] a, int lo, int hi) {
    int i = lo;
    int j = hi + 1;
    int v = a[lo];
    while (true) { 
        while (++i <= hi && nums[i] < v);
        while (--j >= lo && nums[j] > v);

        if (i >= j) {
            break;
        }
        swap(a, i, j);
    }
    swap(a, lo, j);

    // a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
    return j;
}

void swap(int[] a, int i, int j) {
    int temp = a[i];
    a[i] = a[j];
    a[j] = temp;
}
```



### 2、大根堆(前 K 小) / 小根堆（前 K 大)：PriorityQueue（小根堆）

#### 1)算法思路

 保持堆的大小为K，然后遍历数组中的数字，遍历的时候做如下判断：

1. 若目前堆的大小小于K，将当前数字放入堆中。
2. 否则判断当前数字与大根堆堆顶元素的大小关系，如果当前数字比大根堆堆顶还大，这个数就直接跳过；   反之如果当前数字比大根堆堆顶小，先poll掉堆顶，再将该数字放入堆中。

#### 2）代码实现

```java
//前K小
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (k == 0 || arr.length == 0) {
            return new int[0];
        }
        // 默认是小根堆，实现大根堆需要重写一下比较器。
        Queue<Integer> pq = new PriorityQueue<>((v1, v2) -> v2 - v1);
        for (int num: arr) {
            if (pq.size() < k) {
                pq.offer(num);
            } else if (num < pq.peek()) {
                pq.poll();
                pq.offer(num);
            }
        }
        
        // 返回堆中的元素
        int[] res = new int[pq.size()];
        int idx = 0;
        for(int num: pq) {
            res[idx++] = num;
        }
        return res;
    }
}
```





## 参考：

​				[4种解法秒杀TopK（快排/堆/二叉搜索树/计数排序）](https://leetcode-cn.com/problems/zui-xiao-de-kge-shu-lcof/solution/3chong-jie-fa-miao-sha-topkkuai-pai-dui-er-cha-sou/)