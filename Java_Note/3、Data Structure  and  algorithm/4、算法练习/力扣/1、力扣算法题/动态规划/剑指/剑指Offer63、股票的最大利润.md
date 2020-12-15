# 剑指Offer63、股票的最大利润

## 一、题目描述

假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖该股票一次可能获得的最大利润是多少？

 

示例 1:

```
输入: [7,1,5,3,6,4]
输出: 5
解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。
     注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格。
```





## 二、解题方法

### 1、动态规划

dp[i] 代表以 prices[i] 为结尾的子数组的最大利润

前i日最大利润=max ( 前(i−1)日最大利润,  第i日价格 − 前i日最低价格 )

```
class Solution {
    public int maxProfit(int[] prices) {
        if (prices.length == 0) return 0; 
        int len = prices.length;
        int[] dp = new int[len];
        int cost = prices[0];
        dp[0] = 0;
        for (int i = 1; i < len; i++) {
            cost = Math.min(cost, prices[i]);
            dp[i] = Math.max(dp[i - 1], prices[i] - cost);
        }
        return dp[len - 1];
    }
}
```



### 2、1的优化

```
class Solution {
    public int maxProfit(int[] prices) {
        int cost = Integer.MAX_VALUE, profit = 0;
        for(int price : prices) {
            cost = Math.min(cost, price);
            profit = Math.max(profit, price - cost);
        }
        return profit;
    }
}
```

