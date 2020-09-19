

# 一、LRU

LRU 将**最近最少（久未被）使用的页面换出**。



## 1、HashMap + LinkedList（面试写这个）

注意：删除key值要加个(Integer)key，不然它会将key当作list索引

```java
class LRUCache {
    private int capacity;
    private HashMap<Integer,Integer> map;
    private LinkedList<Integer> list;
    
    public LRUCache(int capacity) {
        this.capacity=capacity;
        map=new HashMap<>();
        list=new LinkedList<>();
    }

    public int get(int key) {
        if(map.containsKey(key)){
            //删除key值要加个(Integer)key
            list.remove((Integer)key);
            list.addLast(key);
            return map.get(key);
        }
        return -1;
    }

    public void put(int key, int value) {
        //包含key
        if(map.containsKey(key)){
            list.remove((Integer)key);
            list.addLast(key);
            map.put(key,value);
            return;
        }
        //不包含key,看容量是否已满
        if(list.size()==capacity){
            map.remove(list.removeFirst());
            map.put(key,value);
            list.addLast(key);
        }
        else{
            //没满则直接添加
            map.put(key,value);
            list.addLast(key);
        }
    }
}
```



## 2、LinkedHashMap01

```java
public class LRUCache {

    private Map<Integer,Integer> map;
    private int capacity;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        map = new LinkedHashMap<Integer, Integer>(capacity, 0.75f, true){
            //判断是否删除最老的元素方法，默认返回false，即不删除老数据
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public synchronized int get(int key) {
        return map.getOrDefault(key, -1);
    }

    public synchronized void put(int key, int value) {
        map.put(key, value);
    }

    public synchronized void remove(int key){
        map.remove(key);
    }

    public synchronized int size(){
        return map.size();
    }

    @Override
    public String toString() {
        StringBuilder sb = new StringBuilder();
        for (Map.Entry entry : map.entrySet()) {
            sb.append(String.format("%s:%s ", entry.getKey(), entry.getValue()));
        }
        return sb.toString();
    }
    
    public static void main(String[] args) {

        LRUCache lru = new LRUCache(3);
        lru.put(1,1);
        lru.put(2,2);
        lru.put(3,3);
        System.out.println(lru.map);  //{1=1, 2=2, 3=3}
        lru.get(2);
        System.out.println(lru.map);  //{1=1, 3=3, 2=2}
        lru.put(4,4);
        System.out.println(lru.map);	//{3=3, 2=2, 4=4}
    }    

}
```



## 2、LinkedHashMap02

```java
class LRUCache {

    Map<Integer,Integer> cache = null;

    // 这个是匿名内部类
    // LinkedHashMap的三个构造函数分别是初始容量，扩容因子和是否移除旧的元素
    public LRUCache(int capacity) {
        cache = new LinkedHashMap<>(capacity,0.75f,true){
           //判断是否删除最老的元素方法，默认返回false，即不删除老数据
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public int get(int key) {
        return cache.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        this.cache.put(key,value);
    }
}

```

