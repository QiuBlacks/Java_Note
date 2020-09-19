# java实现页面替换算法

# 一、LRU

LRU 将**最近最少（久未被）使用的页面换出**。

## 1、代码实现01

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



## 2、代码实现02

```
public class LRUCache3 {

    private final int MAX_CACHE_SIZE;
    private final float DEFAULT_LOAD_FACTOR = 0.75f;
    LinkedHashMap map;

    public LRUCache3(int cacheSize) {
        MAX_CACHE_SIZE = cacheSize;
        //根据cacheSize和加载因子计算hashmap的capactiy，+1确保当达到cacheSize上限时不会触发hashmap的扩容，
        int capacity = (int) Math.ceil(MAX_CACHE_SIZE / DEFAULT_LOAD_FACTOR) + 1;

        map = new LinkedHashMap(capacity, DEFAULT_LOAD_FACTOR, true) {

            @Override
            protected boolean removeEldestEntry(Map.Entry eldest) {
                return size() > MAX_CACHE_SIZE;
            }
        };
    }

```





# 二、FIFO

按顺序访问

```
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
```



## 参考文章

[java中LRU缓存实现](https://www.php.cn/java/base/436738.html)