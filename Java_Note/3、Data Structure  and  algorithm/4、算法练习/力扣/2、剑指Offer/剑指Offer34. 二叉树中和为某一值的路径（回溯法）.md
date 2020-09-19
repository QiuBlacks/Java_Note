## 剑指Offer34. 二叉树中和为某一值的路径（回溯法）

## 一、题目介绍

输入一棵二叉树和一个整数，打印出二叉树中节点值的和为输入整数的所有路径。从树的根节点开始往下一直到叶节点所经过的节点形成一条路径


```
示例:
给定如下二叉树，以及目标和 sum = 22

              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
返回:
[
   [5,4,11,2],
   [5,8,4,5]
]
```



## 二、解决思路

## 1、回溯算法

值得注意的是，记录路径时若直接执行 res.add(path) ，则是将 path 对象加入了 res ；后续 path 改变时， res 中的 path 对象也会随之改变

正确做法：res.add(list(path)) ，相当于复制了一个 path 并加入到 res 

```java
class Solution {
    LinkedList<List<Integer>> res = new LinkedList<>();
    LinkedList<Integer> path = new LinkedList<>();
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        recur(root, sum);
        return res;
    }
    void recur(TreeNode root, int tar) {
        //排除不合适的选择
        if(root == null) return;
        //做选择
        path.add(root.val);
        tar -= root.val;
        //添加路径
        if(tar == 0 && root.left == null && root.right == null)
            res.add(new LinkedList(path));
        //递归
        recur(root.left, tar);
        recur(root.right, tar);
        //撤销选择
        path.removeLast();
    }
}
```

参考：https://leetcode-cn.com/problems/er-cha-shu-zhong-he-wei-mou-yi-zhi-de-lu-jing-lcof/solution/mian-shi-ti-34-er-cha-shu-zhong-he-wei-mou-yi-zh-5/ <br>



## 2、DFS+递归

```java
class Solution {
	public List<List<Integer>> pathSum(TreeNode root, int sum) {
		List<List<Integer>> lists = new ArrayList<>();
		if(root == null) return lists;
		findPath(new ArrayList<Integer>(),root,sum,0,lists);
		return lists;
	}
	/*
	 * path:用于存放当前节点所在的路径（随着遍历一直在变动）
	 */
	private static void findPath(List<Integer> path, TreeNode node, int sum, int tempSum,List<List<Integer>> lists) {
		//到当前节点位置的路径的节点值的和
		tempSum += node.val;
		//
		path.add(node.val);
		if(tempSum == sum && node.left == null &&node.right == null) {
			//得到一个符合要求的路径时，创建一个新的ArrayList，拷贝当前路径到其中，并添加到lists中
			lists.add(new ArrayList(path));
		}
		if(node.left != null) {
			findPath(path,node.left,sum,tempSum,lists);
			//递归结束时，该留的路径已经记录了，不符合的路径也都不用理，删掉当前路径节点的值
			path.remove(path.size()-1);
		}
		if(node.right != null) {
			findPath(path,node.right,sum,tempSum,lists);
			//递归结束时，该留的路径已经记录了，不符合的路径也都不用理，删掉当前路径节点的值
			path.remove(path.size()-1);
		}
	}
}

```