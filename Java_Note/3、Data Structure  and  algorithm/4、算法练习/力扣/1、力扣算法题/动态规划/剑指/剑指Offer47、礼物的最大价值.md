# 剑指Offer47、礼物的最大价值

## 一、题目描述

在一个 m*n 的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于 0）。你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格、直到到达棋盘的右下角。给定一个棋盘及其上面的礼物的价值，请计算你最多能拿到多少价值的礼物？

 

示例 1:

```
输入: 
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物
```





## 二、解题方法

### 1、DP

```
class Solution {
    public int maxValue(int[][] grid) {
        if (grid.length == 0) return 0;
        int row = grid.length;
        int col = grid[0].length;
        int[][] value = grid;
        for (int i = 1; i < col; i++) {
            value[0][i] += value[0][i - 1];
        }
        for (int j = 1; j < row; j++) {
            value[j][0] += value[j - 1][0];
        }
        for (int i = 1; i < row; i++) {
            for (int j = 1; j < col; j++) {
                value[i][j] += Math.max(value[i - 1][j], value[i][j - 1]);
            }
        }

        return value[row - 1][col - 1];
    }
}
```

