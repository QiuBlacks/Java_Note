# collect()



```java
class Apple {
    private Integer id;
    private String name;
    private BigDecimal money;
    private Integer num;
    public Apple(Integer id, String name, BigDecimal money, Integer num) {
        this.id = id;
        this.name = name;
        this.money = money;
        this.num = num;
    }
}

public static void main(String[] args) {
        //存放apple对象集合
        List<Apple> appleList = new ArrayList<>();

        Apple apple1 =  new Apple(1,"苹果1",new BigDecimal("3.25"),10);
        Apple apple12 = new Apple(1,"苹果2",new BigDecimal("1.35"),20);
        Apple apple2 =  new Apple(2,"香蕉",new BigDecimal("2.89"),30);
        Apple apple3 =  new Apple(3,"荔枝",new BigDecimal("9.99"),40);

        appleList.add(apple1);
        appleList.add(apple12);
        appleList.add(apple2);
        appleList.add(apple3);
    
}
```



## 分组

```java
//List 以ID分组 Map<Integer,List<Apple>>
Map<Integer, List<Apple>> groupBy = appleList.stream().collect(Collectors.groupingBy(Apple::getId));
```



## List转Map：toMap（map的key值，）

如果map的key重复会报错Duplicate key

```java
Map<Integer, Apple> map = appleList.stream().collect(Collectors.toMap(Apple::getId, Apple::getName));

//Exception in thread "main" java.lang.IllegalStateException: Duplicate key
```

我们可以这样写

```java
Map<Integer, String> map = appleList.stream()
    .collect(Collectors.toMap(Apple::getId, Apple::getName, (s1, s2) -> s1));
 	
 	
//1:苹果1
//2:香蕉
//3:荔枝
```

如果想把重复的也显示出来，则：

```java
 Map<Integer, String> map = appleList.stream()
 	.collect(Collectors.toMap(Apple::getId, Apple::getName, (s1, s2) -> s1 + "," + s2));
 	
//1:苹果1,苹果2
//2:香蕉
//3:荔枝
```



