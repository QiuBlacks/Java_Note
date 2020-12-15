# Lambda表达式（了解）

# 一、基础知识

## 1、基本介绍

Lambda表达式的本质只是一个"语法糖",简化线程(少次)的使用，由编译器推断并帮你转换，包装为常规的代码,因此你可以使用更少的代码来实现同样的功能

## 2、基本语法

- (parameters) -> expression
- (parameters) ->{ statements; }

## 3、简单实例

```java
// 1. 不需要参数,返回值为 5 
() -> 5  
// 2. 接收一个参数(数字类型),返回其2倍的值
x -> 2 * x 
 // 3. 接受2个参数(数字),并返回他们的差值 
 (x, y) -> x – y  
// 4. 接收2个int型整数,返回他们的和
(int x, int y) -> x + y  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)
(String s) -> System.out.print(s)
```



## 4、 Lambda 表达式和匿名类之间的区别

- `this` 关键字。对于匿名类 `this` 关键字解析为匿名类，而对于 Lambda 表达式，`this` 关键字解析为包含写入 Lambda 的类。
- 编译方式。Java 编译器编译 Lambda 表达式时，会将其转换为类的私有方法，再进行动态绑定。



# 二、双冒号 ：：

## 1、基本介绍

双冒号（`::`）操作符是 Java 中的**方法引用**。当们使用一个方法的引用时，目标引用放在 `::` 之前，目标引用提供的方法名称放在 `::` 之后，即 `目标引用::方法`

比如：

```
Person::getAge;
```



# 三、功能接口

在 Java 中，功能接口（Functional interface）指**只有一个抽象方法的接口**。

可以使用Lambda调用功能接口的方法

例如：

```
Runnable r = () -> System.out.println("hello world");
```



## 自定义Lambda接口

`@FunctionalInterface` 是在 Java 8 中添加的一个新注解，用于指示接口类型，声明接口为 Java 语言规范定义的功能接口。

```java
package com.wuxianjiezh.demo.lambda;

@FunctionalInterface
public interface WorkerInterface {

    public void doWork();
    //果我们尝试在其中添加一个抽象方法，则会抛出编译时错误。
   // public void doMoreWork();
}

class WorkTest {

    public static void main(String[] args) {
        // 通过匿名内部类调用
        WorkerInterface work = new WorkerInterface() {
            @Override
            public void doWork() {
                System.out.println("通过匿名内部类调用");
            }
        };
        work.doWork();

        // 通过 Lambda 表达式调用
        // Lambda 表达式实际上是一个对象。
        // 我们可以将 Lambda 表达式赋值给一个变量，就可像其它对象一样调用。
        work = ()-> System.out.println("通过 Lambda 表达式调用");
        work.doWork();
    }
}
```



# 四、Lambda 表达式的常用例子

## 线程初始化

```
// Old way
new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello world");
    }
}).start();

// New way
new Thread(
    () -> System.out.println("Hello world")
).start();
```

## 遍例输出（方法引用）

```
// old way
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);
for (Integer n : list) {
    System.out.println(n);
}

// 使用 -> 的 Lambda 表达式
list.forEach(n -> System.out.println(n));

// 使用 :: 的 Lambda 表达式
list.forEach(System.out::println);
```

## 逻辑操作

```
public class Main {

    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7);

        System.out.print("输出所有数字：");
        evaluate(list, (n) -> true);

        System.out.print("不输出：");
        evaluate(list, (n) -> false);

        System.out.print("输出偶数：");
        evaluate(list, (n) -> n % 2 == 0);

        System.out.print("输出奇数：");
        evaluate(list, (n) -> n % 2 == 1);

        System.out.print("输出大于 5 的数字：");
        evaluate(list, (n) -> n > 5);
    }

    public static void evaluate(List<Integer> list, Predicate<Integer> predicate) {
        for (Integer n : list) {
            if (predicate.test(n)) {
                System.out.print(n + " ");
            }
        }
        System.out.println();
    }
}
```

