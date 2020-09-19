# LinkedHashMap

# 一、基本介绍

## 1、定义

在继承HashMap的基础上，采用双向链表(doubly-linked list)的形式将所有entry连接起来，**保证元素的迭代顺序跟插入顺序相同**

键值对允许为空，允许重复数据，线程不安全

<img src="https://gitee.com/BlacksJack/picture-bed/raw/master/img/image-20200909104815638.png" alt="image-20200909104815638" style="zoom: 67%;" />

## 2、主要属性

### 2.1 两个重要属性

```java
//双向链表的头节点,遍历的开始
private transient Entry<K,V> header;

//true表示最近最少使用次序，false表示插入顺序
private final boolean accessOrder;
```

LinkedHashMap存储数据是有序的，而且分为两种：插入顺序和访问顺序（get、put）

accessOrder：

​		false：插入顺序排序

​		true：访问顺序排序，被访问的元素都被提到最后面去了

### 2.2  前后节点

```java
static class Entry<K,V> extends HashMap.Node<K,V> {
    Entry<K,V> before, after;
    
    Entry(int hash, K key, V value, Node<K,V> next) {
        super(hash, key, value, next);
    }
}
```

**next是用于维护HashMap指定table位置上连接的Entry的顺序的，before、After是用于维护Entry插入的先后顺序的**。



## 3、主要方法

### 3.1 构造方法

```
    public LinkedHashMap(int initialCapacity,
                         float loadFactor,
                         boolean accessOrder) {
        super(initialCapacity, loadFactor);
        this.accessOrder = accessOrder;
    }
```





## 4、具体例子

```java
    Map<String, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("name1", "josan1");
        linkedHashMap.put("name2", "josan2");
        linkedHashMap.put("name3", "josan3");
                // LinkedHashMap没有重写该方法，调用的HashMap中的entrySet方法
        Set<Entry<String, String>> set = linkedHashMap.entrySet();
        Iterator<Entry<String, String>> iterator = set.iterator();
        while(iterator.hasNext()) {
            Entry entry = iterator.next();
            String key = (String) entry.getKey();
            String value = (String) entry.getValue();
            System.out.println("key:" + key + ",value:" + value);
        }
```



# 二、源码分析

## 1、get

```java
  public V get(Object key) {
        Node<K,V> e;
        if ((e = getNode(hash(key), key)) == null)
            return null;
        //如果是按访问顺序排序的，就把当前节点加入最后
        if (accessOrder)
            afterNodeAccess(e);
        return e.value;
    }
    
void recordAccess(HashMap<K,V> m) {
    LinkedHashMap<K,V> lm = (LinkedHashMap<K,V>)m;
    // 如果定义了LinkedHashMap的迭代顺序为访问顺序，
    // 则删除以前位置上的元素，并将最新访问的元素添加到链表表头。  
    if (lm.accessOrder) {
        lm.modCount++;
        remove();
        addBefore(lm.header);
    }
}
```



## 2、put

LinkedHashMap并未重写父类HashMap的put方法，而是重写了父类HashMap的put方法调用的子方法void **recordAccess**(HashMap m) ，void **addEntry**(int hash, K key, V value, int bucketIndex) 和void **createEntry**(int hash, K key, V value, int bucketIndex)，提供了自己特有的双向链接列表的实现。



```java
void addEntry(int hash, K key, V value, int bucketIndex) {
    //调用create方法，将新元素以双向链表的的形式加入到映射中
    createEntry(hash, key, value, bucketIndex);

    // Remove eldest entry if instructed, else grow capacity if appropriate
    // 删除最近最少使用元素的策略定义
    Entry<K,V> eldest = header.after;
    if (removeEldestEntry(eldest)) {
        removeEntryForKey(eldest.key);
    } else {
        if (size >= threshold)
            resize(2 * table.length);
    }
}

void createEntry(int hash, K key, V value, int bucketIndex) {
    HashMap.Entry<K,V> old = table[bucketIndex];
    Entry<K,V> e = new Entry<K,V>(hash, key, value, old);
    table[bucketIndex] = e;
    // 调用元素的addBrefore方法，将元素加入到哈希、双向链接列表。  
    e.addBefore(header);
    size++;
}

private void addBefore(Entry<K,V> existingEntry) {
    after  = existingEntry;
    before = existingEntry.before;
    before.after = this;
    after.before = this;
}
```

