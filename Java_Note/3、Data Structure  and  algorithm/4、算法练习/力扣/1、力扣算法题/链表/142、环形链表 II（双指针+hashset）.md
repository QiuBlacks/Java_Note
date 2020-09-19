# 142、环形链表 II（双指针+hashset）

## 一、题目描述

给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，我们使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

说明：不允许修改给定的链表。

示例 1：

```
输入：head = [3,2,0,-4], pos = 1
输出： 1
解释：链表中有一个环，其尾部连接到第二个节点。
```





## 二、解题方法

### 1、HashSet

如果我们用一个 Set 保存已经访问过的节点，我们可以遍历整个列表并返回第一个出现重复的节点。

```
public class Solution {
    public ListNode detectCycle(ListNode head) {
        Set<ListNode> visited = new HashSet<ListNode>();

        ListNode node = head;
        while (node != null) {
            if (visited.contains(node)) {
                return node;
            }
            visited.add(node);
            node = node.next;
        }
        return null;
    }
}
```

执行用时：6 ms, 在所有 Java 提交中击败了7.66%的用户

内存消耗：41 MB, 在所有 Java 提交中击败了6.57%的用户



### 2、双指针

### 2.1  算法思想

设a为头节点到环入口的距离，b为环的长度；f为快指针的步数，s为慢指针的

第一次相遇：

​		f=2s, f  = 2s+b;  得：f  = 2nb，s=nb

​		又：所有到达环入口走的步数为 a+mb

第二次相遇时：

​		由于第一次s走了nb，所以再走a步不就到达环入口了

​		令f=head，然后走a步，就是第二次相遇，也就是在环入口了

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        ListNode fast = head, slow = head;
        //判断是否成环
        while (true) {
            if (fast == null || fast.next == null) return null;
            fast = fast.next.next;
            slow = slow.next;
            //第一次相遇时结束
            if (fast == slow) break;
        }
        
        fast = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return fast;
    }
}
```

执行用时：0 ms, 在所有 Java 提交中击败了100.00%的用户

内存消耗：40.1 MB, 在所有 Java 提交中击败了25.50%的用户