# 剑指Offer27、二叉树的镜像

## 一、题目描述

请完成一个函数，输入一个二叉树，该函数输出它的镜像。

```
例如输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1
```

**示例 1：**

```
输入：root = [4,2,7,1,3,6,9]
输出：[4,7,2,9,6,3,1]
```



## 二、解题方法

### 1、递归01

```
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return null;
        TreeNode tmp = root.left;
        root.left = mirrorTree(root.right);
        root.right = mirrorTree(tmp);
        return root;
    }
}
```

执行用时：0 ms, 在所有 Java 提交中击败了100.00%的用户

内存消耗：36.7 MB, 在所有 Java 提交中击败了5.04%的用户



### 2、栈或者队列

```
class Solution {
    public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return null;
        Stack<TreeNode> stack = new Stack<>() {{ add(root); }};
        while(!stack.isEmpty()) {
            TreeNode node = stack.pop();
            if(node.left != null) stack.add(node.left);
            if(node.right != null) stack.add(node.right);
            TreeNode tmp = node.left;
            node.left = node.right;
            node.right = tmp;
        }
        return root;
    }
}
```





### 自制递归

```
class Solution {
      public TreeNode mirrorTree(TreeNode root) {
        if(root == null) return null;
        TreeNode left = root.left;
        TreeNode right = root.right;
        root.left = right;
        root.right = left;
        mirrorTree(root.left);
        mirrorTree(root.right);
        return root;
    }
}
```

执行用时：0 ms, 在所有 Java 提交中击败了100.00%的用户

内存消耗：36.8 MB, 在所有 Java 提交中击败了5.04%的用户

