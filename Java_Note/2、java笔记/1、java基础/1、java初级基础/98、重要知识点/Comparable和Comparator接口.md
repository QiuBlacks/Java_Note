# Comparable和Comparator接口

## 一、Comparable接口（内比较器）

### 1、基本介绍

  Comparable可以认为是一个内比较器，实现了Comparable接口的类有一个特点，就是这些类是可以和自己比较的。

 Comparable是排序接口。若一个类实现了Comparable接口，就意味着该类支持**自动排序**。实现了Comparable接口的类的对象的列表或数组可以通过**Collections.sort或Arrays.sort**进行自动排序。此外，实现此接口的对象可以用作有序映射中的键或有序集合中的集合，无需指定比较器。该接口定义如下：

```Java
public interface Comparable<T>  {  
	public int compareTo (T o); 
		}
```

注意：实现接口Comparable**<类型>**，且Arrays.sort()排序的参数为**数组**

### 2、返回值

compareTo方法的返回值是int，有三种情况：

- 比较者大于被比较者（也就是compareTo方法里面的对象），那么返回正整数	
- 比较者等于被比较者，那么返回0
- 比较者小于被比较者，那么返回负整数

### 3、实例

```Java
public class Person implements Comparable<Person>
{
    String name;
    int age;
    public Person(String name, int age)
    {
        super();
        this.name = name;
        this.age = age;
    }
    public String getName()
    {
        return name;
    }
    public int getAge()
    {
        return age;
    }
    @Override
    public int compareTo(Person p)
    {
        return this.age-p.getAge();
    }
    public static void main(String[] args)
    {
        Person[] people=new Person[]{new Person("xujian", 20),new Person("xiewei", 10)};
        System.out.println("排序前");
        for (Person person : people)
        {
            System.out.print(person.getName()+":"+person.getAge());
        }
        Arrays.sort(people);
        System.out.println("\n排序后");
        for (Person person : people)
        {
            System.out.print(person.getName()+":"+person.getAge());
        }
    }
}
```



## 二、Comparator接口（外比较器）

### 1、基本介绍

Comparator是比较接口，我们如果需要控制某个类的次序，而该类本身不支持排序(即没有实现Comparable接口)，那么我们就可以建立一个“该类的比较器”来进行排序，这个“比较器”只需要实现Comparator接口即可。也就是说，我们可以通过实现Comparator来新建一个比较器，然后通过这个比较器对类进行排序。

```java
//进行类之间的排序 
public interface Comparator<T> {  
    int compare(T o1, T o2);  
    boolean equals(Object obj); 
}
```

### 2、使用方法

**作为一个参数**传递到Collections.sort和Arrays.sort方法来指定某个类对象的排序方式。同时它也能为sorted set和sorted map指定排序方式。

```Java
public class MyComparator implements Comparator<User> {
    @Override
    public int compare(User o1, User o2) {
        return o1.getName().charAt(0)-o2.getName().charAt(0);
    }
}
```



### 3、使用情况

Comparator可以认为是是一个外比较器，个人认为有两种情况可以使用实现Comparator接口的方式：

 1、一个对象不支持自己和自己比较（没有实现Comparable接口），但是又想对两个对象进行比较

 2、一个对象实现了Comparable接口，但是开发者认为compareTo方法中的比较方式并不是自己想要的那种比较方式



### 4、可以通过实现Comparator接口重写compare（）来做个自定义比较器

Comparator接口里面有一个compare方法，方法有两个参数T o1和T o2，是泛型的表示方式，分别表示待比较的两个对象，方法返回值和Comparable接口一样是int，有三种情况：

 1、o1大于o2，返回正整数，降序排序

 2、o1等于o2，返回0

 3、o1小于o3，返回负整数，升序排序









## 三、comparable 和 Comparator的区别

- comparable接口实际上是出自java.lang包 它有一个 `compareTo(Object obj)`方法用来排序
- comparator接口实际上是出自 java.util 包它有一个`compare(Object obj1, Object obj2)`方法用来排序

一般我们需要对一个集合使用自定义排序时，我们就要重写compareTo()方法或compare()方法，当我们需要对某一个集合实现两种排序方式，比如一个song对象中的歌名和歌手名分别采用一种排序方法的话，我们可以重写compareTo()方法和使用自制的Comparator方法或者以两个Comparator来实现歌名排序和歌星名排序，第二种代表我们只能使用两个参数版的 Collections.sort().



## 四、实例

定义个User类实现Comparable接口

```Java
public class User implements Serializable, Comparable<User> {
    private static final long serialVersionUID = 1L;
    private int age;
    private String name;
    public User (){}
    public User (int age, String name){
        this.age = age;
        this.name = name;
    }
    public int getAge() {
        return age;
    }
    public void setAge(int age) {
        this.age = age;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public int compareTo(User o) {
        return this.age - o.age;
    }
    @Override
    public String toString() {
        return "[user={age=" + age + ",name=" + name + "}]";
    }
}
```

定义MyComparator实现Comparator接口：按照字母顺序排列

```Java
public class MyComparator implements Comparator<User> {
    @Override
    public int compare(User o1, User o2) {
        return o1.getName().charAt(0)-o2.getName().charAt(0);
    }
}
```

定义Main方法

```Java
public class Main {
    public static void main(String[] args) {
        User u1 = new User(12, "xiaohua");
        User u2 = new User(10, "abc");
        User u3 = new User(15,"ccc");
        User[] users = {u1,u2,u3};
        System.out.print("数组排序前：");
        printArray(users);
        System.out.println();
        Arrays.sort(users);
        System.out.print("数组排序1后：");
        printArray(users);
        System.out.println();
        Arrays.sort(users, new MyComparator());
        System.out.print("数组排序2后：");
        printArray(users);
        System.out.println();
        Arrays.sort(users, Comparator.reverseOrder());// 针对内置的排序进行倒置
        System.out.print("数组排序3后：");
        printArray(users);
    }
    public static void printArray (User[] users) {
        for (User user:users) {
            System.out.print(user.toString());
        }
    }
}
```

运行结果：

```Java
数组排序前：[user={age=12,name=xiaohua}][user={age=10,name=abc}][user={age=15,name=ccc}]
数组排序1后：[user={age=10,name=abc}][user={age=12,name=xiaohua}][user={age=15,name=ccc}]
数组排序2后：[user={age=10,name=abc}][user={age=15,name=ccc}][user={age=12,name=xiaohua}]
数组排序3后：[user={age=15,name=ccc}][user={age=12,name=xiaohua}][user={age=10,name=abc}]
```





## 参考文献：

[Java中Comparable和Comparator区别小结](https://www.cnblogs.com/xujian2014/p/5215082.html)

[Comparable和Comparator的区别](https://www.cnblogs.com/szlbm/p/5504634.html)



