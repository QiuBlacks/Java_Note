[TOC]



# Iterator

## 一、基本介绍

### 1、定义

Iterator是一个接口，它是集合的迭代器。集合可以通过Iterator去遍历集合中的元素。

```Java
public interface Iterator<E> {}
```

### 2、常用API

- boolean hasNext()：判断集合里是否存在下一个元素。如果有，hasNext()方法返回 true。
- Object next()：返回集合里下一个元素。
- void remove()：删除集合里上一次next方法返回的元素。



### 3、实例

```java
public class IteratorExample {
    public static void main(String[] args) {
        ArrayList<String> a = new ArrayList<String>();
        a.add("aaa");
        a.add("bbb");
        a.add("ccc");
        System.out.println("Before iterate : " + a);
        Iterator<String> it = a.iterator();
        while (it.hasNext()) {
            String t = it.next();
            if ("bbb".equals(t)) {
                it.remove();
            }
        }
        System.out.println("After iterate : " + a);
    }
}
```

结果：

```java
Before iterate : [aaa, bbb, ccc]
After iterate : [aaa, ccc]
```

#### 注意：

- Iterator只能单向移动。
- Iterator.remove()是唯一安全的方式来在迭代过程中修改集合；如果在迭代过程中以任何其它的方式修改了基本集合将会产生未知的行为。而且每调用一次`next()`方法，`remove()`方法只能被调用一次，如果违反这个规则将抛出一个异常。





# ListIterator

## 一、基本介绍

### 1、定义

**ListIterator是一个功能更加强大的迭代器, 它继承于Iterator接口，**只能用于各种List类型的访问。

可以通过调用`listIterator()`方法产生一个指向List开始处的ListIterator, 还可以调用`listIterator(n)`方法创建一个一开始就指向列表索引为n的元素处的ListIterator。

```java
public interface ListIterator<E> extends Iterator<E> {
    void add(E e);
    boolean hasNext();
    boolean hasPrevious();
    E next();			
    E previous();      //返回列表中的上一个元素，并向后移动光标位置
    int previousIndex();
    int nextIndex();
    void remove();   //从列表中删除由 next()或 previous()返回的最后一个元素
    void set(E e);   //用指定的元素替换由 next()或 previous()返回的最后一个元素
  
}
```

- 双向移动（向前/向后遍历）.
- 产生相对于迭代器在列表中指向的当前位置的前一个和后一个元素的索引.
- 可以使用`set()`方法替换它访问过的最后一个元素.
- 可以使用`add()`方法在`next()`方法返回的元素之前或`previous()`方法返回的元素之后插入一个元素

### 2、实例

```java
public class ListIteratorExample {

    public static void main(String[] args) {
        ArrayList<String> a = new ArrayList<String>();
        a.add("aaa");
        a.add("bbb");
        a.add("ccc");
        System.out.println("Before iterate : " + a);
        ListIterator<String> it = a.listIterator();
        while (it.hasNext()) {
            System.out.println(it.next() + ", " + it.previousIndex() + ", " + it.nextIndex());
        }
        while (it.hasPrevious()) {
            System.out.print(it.previous() + " ");
        }
        System.out.println();
        it = a.listIterator(1);
        while (it.hasNext()) {
            String t = it.next();
            System.out.println(t);
            if ("ccc".equals(t)) {
                it.set("nnn");
            } else {
                it.add("kkk");
            }
        }
        System.out.println("After iterate : " + a);
    }
}
```

结果：

```java
Before iterate : [aaa, bbb, ccc]
aaa, 0, 1
bbb, 1, 2
ccc, 2, 3
ccc bbb aaa 
bbb
ccc
After iterate : [aaa, bbb, kkk, nnn]
```



### 3、注意：迭代器的位置

![image-20200522100341770](E:\black user\Java\有道云截图\image-20200522100341770.png)





# Iterator和ListIterator异同点



## 一、相同点

- 都是迭代器，当需要对集合中元素进行遍历不需要干涉其遍历过程时，这两种迭代器都可以使用

- 都可实现删除对象remove()



## 二、不同点

### 1、使用范围

Iterator可以应用于所有的集合，Set、List和Map和这些集合的子类型。而ListIterator只能用于List及其子类型

### 2、add()

ListIterator有`add()`方法，可以向List中添加对象，而Iterator不能

### 3、逆序遍历

ListIterator和Iterator都有`hasNext()`和`next()`方法，可以实现顺序向后遍历。

但是ListIterator有`hasPrevious()`和`previous()`方法，可以实现逆向（顺序向前）遍历。Iterator就不可以。

### 4、定位索引位置

ListIterator可以定位当前的索引位置，`nextIndex()`和`previousIndex()`可以实现。Iterator没有此功能。

### 5、修改对象

ListIterator可以实现对象的修改，`set()`方法可以实现。Iierator仅能遍历，不能修改。



因为ListIterator的这些功能，可以实现对LinkedList等List数据结构的操作。其实，数组对象也可以用迭代器来实现。































