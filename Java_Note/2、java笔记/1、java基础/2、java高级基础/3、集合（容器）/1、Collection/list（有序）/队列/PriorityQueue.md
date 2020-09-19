# PriorityQueue（小根堆）

参考：https://blog.csdn.net/u013309870/article/details/71189189





```
add()		:将指定的元素插入到此优先级队列中。
clear()
contains(Object o) 
comparator() :返回用于为了在这个队列中的元素，或比较null如果此队列根据所述排序natural ordering的元素。 
offer(E e) :将指定的元素插入到此优先级队列中。 
peek() :检索但不删除此队列的头，如果此队列为空，则返回 null
poll()  :检索并删除此队列的头，如果此队列为空，则返回 null
remove(Object o) :从该队列中删除指定元素的单个实例（如果存在）
size() 
```





```
 //Lambda 表达式
 Queue<ListNode> pq = new PriorityQueue<>((v1, v2) -> v1.val - v2.val);
 
 Queue<ListNode> pq = new PriorityQueue<>(new Comparator<Object>() {
        public int compare(Object o1, Object o2) {
            //升序
            return o1-o2;
            //降序
            return o2-o1;
        });
```

