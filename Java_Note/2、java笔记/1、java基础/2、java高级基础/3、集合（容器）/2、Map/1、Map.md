# Map

# 一、基本介绍

## 1、方法

```java
keySet()		//方法返回值是Map中key值的集合；

entrySet()	  //返回值也是返回一个Set集合，此集合的类型为Map.Entry
     
// 是Map声明的一个内部接口，此接口为泛型，键值对定义为Entry<K,V>。
//它表示Map中的一个实体（一个key-value对）。接口中有getKey(),getValue方法。
Map.Entry< >  
```



## 2、遍历

### 2.1  普通遍历

```Java
for (Map.Entry<String, String> entry : map.entrySet()) {   		 								      	 	System.out.println(entry.getKey() + " ：" + entry.getValue()); 
	}

for (int key : map.keySet()){
            
	}
for (int value : map.values()){

	}
```



### 2.2  迭代器遍历

1）使用keySet()遍历

```java
Iterator<String> iterator = map.keySet().iterator();
while (iterator.hasNext()) {
    String key = iterator.next();
    System.out.println(key + "　：" + map.get(key));
}
```

2）使用entrySet()遍历

```java
//返回一个键值对
Iterator<Map.Entry<String, String>> iterator = map.entrySet().iterator();
while (iterator.hasNext()) {
    Map.Entry<String, String> entry = iterator.next();
    System.out.println(entry.getKey() + "　：" + entry.getValue());
}
```



# 二、实现类