## 剑指Offer59 - II 队列的最大值



## 一、题目描述

请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。

```
输入: 
["MaxQueue","push_back","push_back","max_value","pop_front","max_value"]
[[],[1],[2],[],[],[]]
输出: [null,null,null,2,1,2]

输入: 
["MaxQueue","pop_front","max_value"]
[[],[],[]]
输出: [null,-1,-1]
```











## 二、解题思路

### 1、队列+链表





### 2、双端队列

思路挺好的，但错误的

```
class MaxQueue {
    Deque<Integer> deque ;
    Queue<Integer> que;
    public MaxQueue() {
        deque= new LinkedList<>();
        que=new LinkedList<>();
    }
    
    public int max_value() {
        return deque.size()>0?deque.peek():-1;
    }
    
    public void push_back(int value) {
       que.offer(value);
        while(deque.peekLast()<value&&deque.size()>0) deque.pollLast();
        deque.offerLast(value);
    }
    
    public int pop_front() {
         int temp=que.size()>0?que.poll():-1;
         if(deque.size()>0&&deque.peek()==temp) deque.pop();
         return temp;
    }
}
```

