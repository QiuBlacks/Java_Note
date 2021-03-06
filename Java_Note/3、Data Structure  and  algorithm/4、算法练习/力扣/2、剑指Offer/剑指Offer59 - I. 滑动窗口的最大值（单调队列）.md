## 剑指Offer59 - I. 滑动窗口的最大值（单调队列）

## 一、题目介绍

给定一个数组 `nums` 和滑动窗口的大小 `k`，请找出==所有滑动窗口==里的最大值。

```
输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
输出: [3,3,5,5,6,7] 
解释: 

  滑动窗口的位置                最大值
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

## 二、解题思路

### 1、自制暴力法

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0 || k == 0) return new int[0];
        int[] res=new int[nums.length-k+1];
        int i=0;
        while(i<nums.length-k+1){
            int max=nums[i];
            for(int j=i+k-1;j>i;j--){
                max=Math.max(max,nums[j]);
            }
            res[i]=max;
            i++;
        }
        return res;
    }
}
```

### 2、单调双端队列

#### 1）算法思路

保持队列递减；即第一个最大

队列里存放的一定是本次窗口的值（但不一定是全部，因为要保持递减）

注意：如果队列头部不包含在窗口内，要删掉

#### 2）算法流程：

- 初始化： 双端队列 dequedeque ，结果列表 resres ，数组长度 nn ；

- 滑动窗口： 左边界范围 i \in [1 - k, n + 1 - k]i∈[1−k,n+1−k] ，右边界范围 j \in [0, n - 1]j∈[0,n−1] ；

​				若 i > 0i>0 且 队首元素 deque[0]deque[0] == 被删除元素 nums[i - 1]nums[i−1] ：则队首元素出队；

​				删除 dequedeque 内所有 < nums[j]<nums[j] 的元素，以保持 dequedeque 递减；

​				将 nums[j]nums[j] 添加至 dequedeque 尾部；
​						若已形成窗口（即 i \geq 0i≥0 ）：将窗口最大值（即队首元素 deque[0]deque[0] ）添加至列表 resres 。

- 返回值： 返回结果列表 resres 。

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0 || k == 0) return new int[0];
        Deque<Integer> deque = new LinkedList<>();
        int[] res = new int[nums.length - k + 1];
        for(int j = 0, i = 1 - k; j < nums.length; i++, j++) {
            if(i > 0 && deque.peekFirst() == nums[i - 1]) deque.removeFirst(); // 删除 deque 中对应的 nums[i-1]
            while(!deque.isEmpty() && deque.peekLast() < nums[j]) deque.removeLast(); // 保持 deque 递减
            deque.addLast(nums[j]);
            if(i >= 0) res[i] = deque.peekFirst();  // 记录窗口最大值
        }
        return res;
    }
}
```

### 3、高级暴力法

```Java
    public int[] maxSlidingWindow(int[] nums, int k) {
        int len = nums.length;
        if (len == 0){
            return new int[0];
        }
        //定义结果数组
        int[] res = new int[len - k + 1];
        //maxInd记录每次最大值的下标，max记录最大值
        int maxInd = -1, max = Integer.MIN_VALUE;

        for (int i = 0; i < len - k + 1; i++) {
            //判断最大值下标是否在滑动窗口的范围内
            if (maxInd >= i){
                //存在就只需要比较最后面的值是否大于上一个窗口最大值
                if (nums[i + k - 1] > max){
                    max = nums[i + k - 1];
                    //更新最大值下标
                    maxInd = i + k - 1;
                }
            }
            //如果不在就重新寻找当前窗口最大值
            else {
                max = nums[i];
                for (int j = i; j < i + k; j++) {
                    if (max < nums[j]) {
                        max = nums[j];
                        maxInd = j;
                    }
                }
            }
            res[i] = max;
        }
        return res;
    }
```

