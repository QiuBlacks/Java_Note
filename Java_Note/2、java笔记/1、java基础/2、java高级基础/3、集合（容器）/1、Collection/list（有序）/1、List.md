# List（有序集合）

# 一、常用方法

## 1、list.size()

for循环中，当res.add()的同时，他的res.size也会不断的增大，应该先取一个变量存储大小

```java
public static void main(String[] args) {
        List<Integer> res = new LinkedList<>();
        res.add(1);

        for(int i = 0; i < res.size(); i++){
            System.out.println("i =="+ i + "res.size=="  + res.size());
            res.add(i);
         }
    }

public static void main(String[] args) {
        List<Integer> res = new LinkedList<>();
        res.add(1);
		int size = res.size();
        for(int i = 0; i < size; i++){
            System.out.println("i =="+ i + "res.size=="  + res.size());
            res.add(i);
         }
    }
```



## 2、List.romove()详解

### 2.1、源码分析

```java
// 删除ArrayList指定位置的元素
    public E remove(int index) {
        RangeCheck(index);//检查index是否超出list大小范围，否则抛出异常
        modCount++;
        E oldValue = (E) elementData[index];//elementData是实现list的数组
        int numMoved = size - index - 1;//当执行删除操作是后面的元素全部向前面移动一位
        if (numMoved > 0)
            System.arraycopy(elementData, index+1, elementData, index,
                 numMoved);
        elementData[--size] = null;
        return oldValue;
    }
    // 删除ArrayList的指定元素
    public boolean remove(Object o) {
        if (o == null) {
        　　for (int index = 0; index < size; index++)
           　　if (elementData[index] == null) {
               　　fastRemove(index);
            　　　 return true;
            }
        } else {
            for (int index = 0; index < size; index++)
           　　 if (o.equals(elementData[index])) {
           　　     fastRemove(index);
           　　     return true;
         　　   }
        }
        return false;
   }
 　//快速删除第index个元素
　　private void fastRemove(int index) {
        modCount++;  
        int numMoved = size - index - 1;  
        if (numMoved > 0)  
            System.arraycopy(elementData, index+1, elementData, index,  
                             numMoved);  
        elementData[--size] = null; 
   } 
```

源码可知，List在删除指定位置的对象时，执行删除操作是**后面的元素全部向前面移动一位**

因为，当你remove掉一个对象时，list的就少了一个 index 0的被remove了，之前index 1的数据就自动变为index 0了。arrayList是有顺序数组，从0开始。如果从前开始删除实际上就相当于跳着删除了。



### 2.2、案例

### 1）  代码

```
 public static void main(String[] args) {
        List<CaseHead> list=new ArrayList<CaseHead>();
        CaseHead caseHead1=new CaseHead();
        caseHead1.setCaseid("a");
        CaseHead caseHead2=new CaseHead();
        caseHead2.setCaseid("b");
        CaseHead caseHead3=new CaseHead();
        caseHead3.setCaseid("c");
        CaseHead caseHead4=new CaseHead();
        caseHead4.setCaseid("d");
        CaseHead caseHead5=new CaseHead();
        caseHead5.setCaseid("e");
        list.add(caseHead1);
        list.add(caseHead2);
        list.add(caseHead3);
        list.add(caseHead4);
        list.add(caseHead5);


        List<String> list2=new ArrayList<String>();
        list2.add("a");
        list2.add("b");

        for (int i = 0; i < list.size(); i++) {
            String caseid=list.get(i).getCaseid();
            for (int j = 0; j <list2.size() ; j++) {
                String l=list2.get(j);
                if (caseid.equals(l)){
                    list.remove(i);
                }
            }


        }

        for (int a = 0; a < list.size(); a++) {
            System.out.println(list.get(a).getCaseid());
        }}
```

根据以上代码，希望得到的结果是 cde 但是运行结果却是bcde



### 2） 解决方法

- list转为迭代器Iterator进行迭代删除

- 倒着删除从后往前遍历删除，从index大的往index小的删
- 每次删除之后i - -自动返回到上一个index开始




## 3、contains(obj)

List调用contains(Object obj)方法时，会遍历List中的每一个元素，然后再调用每个元素的equals()方法去跟contains()方法中的参数进行比较，如果有一个元素的equals()方法返回true则contains()方法返回true，否则所有equals()方法都不返回true，则ontains()方法则返回false。

因此，如果没有重写了Qiu类的equals()方法，输出为false。

```
public static void main(String[] args) {
        Qiu qiu01 = new Qiu("qiu01");
        Qiu qiu02 = new Qiu("qiu01");
        List<Qiu> list = new ArrayList<>();
        list.add(qiu01);

        System.out.println(list.contains(qiu02)); //false
    }
```



# 三、数组和List的转换



除了通过**遍历**的方式将数组一个个存入List中，我们还可以通过Java里面的一些API

但是因为`List`声明时，需要传递一个泛型`<E>`作为形参，`asList()`参数类型也是泛型中的通配类型`<T>`。Java中所有的泛型必须是引用类型。

```
   public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }
```



## 1、引用类型转List

### 1）Arrays.asList（）

这种方式只能转成数组，不能进行remove和add操作

```java
 String[] str = {"a","b","c","d"};
 List<String> list1 = Arrays.asList(str);
 list1.add("2");
 
 /**
 Exception in thread "main" java.lang.UnsupportedOperationException
	at java.util.AbstractList.add(AbstractList.java:148)
	at java.util.AbstractList.add(AbstractList.java:108)
	at Solution.main(Solution.java:17)
*/
```

想要支持remove和add操作，可以这么写：

```
 String[] str = {"a","b","c","d"};
 List<String> list2 = new ArrayList<String>(Arrays.asList(str));
 list2.add("e");
 list2.remove("c");
```

### 2）Collections.addAll(）推荐使用

```
String[] str = {"a","b","c","d"};
List<String> list3 = new ArrayList<>();
Collections.addAll(list3, str);
list3.add("c");
```

### 3） Java9 的 List.of()方法

```
Integer[] array = {1, 2, 3};
List<Integer> list = List.of(array);
System.out.println(list); /* [1, 2, 3] */
/* 不支持基本数据类型 */
```



## 2、基本类型转List

### 1） Java8 的Stream(推荐)

使用stream流，数组的每一个元素都被从int转换成了Integer类型(装箱)，数组就被转成了List类型。

```
public class Test {
   public static void main(String[] args) {
      int[] intArray = { 5, 10, 21 };
      List myList = Arrays.stream(intArray).boxed().collect(Collectors.toList());
   }
}
```



## 3、List转数组

1）

```
String [] str = new String[]{
                "dog", "lazy", "a", "over", "jumps", "fox", "brown", "quick", "A"
       };
 List<String> list = Arrays.asList(str);
 Collections.reverse(list);
str = list.toArray(new String[0]);
```

2）

```
String [] str = new String[]{
	"dog", "lazy", "a", "over", "jumps", "fox", "brown", "quick", "A"
};
List<String> list = Arrays.asList(str);
Collections.reverse(list);
str = (String[]) list.toArray();
```



## 参考文章

[使用List中的remove方法遇到的坑，不信你没有踩过](https://mp.weixin.qq.com/s?__biz=MzA5MTMyNTI0Nw==&mid=2649792054&idx=2&sn=ae731b708019b9e47defcee4ab19dab6&chksm=887a2866bf0da170b44ee376735667726bd965d46c75c609332c70aec62090132681f0045355&scene=126&sessionid=1593735128&key=512f5ec3dfd47d201106c91c6d78b603a26d96ea661107ef79be60afc340014e9a689f65badaa4d501e16381e951948cc759ff06f19d909e2d8123d1f343af01d9ab40aa958d57c09b0d538d001c5175&ascene=1&uin=MTg5MjE1NTkyOQ%3D%3D&devicetype=Windows+10+x64&version=62090529&lang=zh_CN&exportkey=A3bqffc2LqUI616tmYsPwOg%3D&pass_ticket=DsoEm3acI5%2Fpz45V4LJSG%2BgD%2Bs%2Fsjeey5O7dkZOChuViAIzRxJ2h23p%2B%2Fl43lpDS)

