## 剑指Offer52.两个链表的第一个公共点（双指针）

## 一、题目描述

输入两个链表，找出它们的第一个公共节点



## 二、解决方法

### 1、双指针01：两指针同时移动

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        ListNode tempA=headA;
        ListNode tempB=headB;
        while(tempA!=tempB){
            //这里设计很巧妙，让tempA指向headB,考虑了
           tempA= tempA==null?headB:tempA.next;
           tempB= tempB==null?headA:tempB.next;
        }
        return tempA;
    }
}
```

### 2、双指针02

先让比较长的指针移动len(a-b)，再两指针同时移动

```java
public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        int lengthA = getLength(headA), lengthB = getLength(headB);
        ListNode a = headA, b = headB;
        if(lengthA > lengthB){
            for(int i = 0; i < lengthA - lengthB; i++)
                a = a.next;
        } else {
            for(int i = 0; i < lengthB - lengthA; i++)
                b = b.next;
        }
        while(a != b){
            a = a.next;
            b = b.next;
        }
        return a;
    }
    private int getLength(ListNode head){
        int length = 0;
        for(ListNode temp = head; temp != null; temp = temp.next, length++);
        return length;
    }
```

