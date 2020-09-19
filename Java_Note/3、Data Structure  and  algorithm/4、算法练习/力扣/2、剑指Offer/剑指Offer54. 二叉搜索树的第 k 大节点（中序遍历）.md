## 剑指Offer54. 二叉搜索树的第 k 大节点（中序遍历）

## 一、题目描述

给定一棵二叉搜索树，请找出其中第k大的节点



## 二、解题方法

```Java
class Solution {
    int res, k;
    public int kthLargest(TreeNode root, int k) {
        this.k = k; //this.k赋值全局变量k，使得在dfs中能使用
        dfs(root);
        return res;
    }
    void dfs(TreeNode root) {
        if(root == null) return;
        dfs(root.right);
        if(k == 0) return;
        if(--k == 0) res = root.val;
        dfs(root.left);
    }
}
```

