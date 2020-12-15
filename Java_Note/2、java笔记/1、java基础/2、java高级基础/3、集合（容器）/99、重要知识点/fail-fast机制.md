# fail-fast机制

# 一、基础知识

## 一、基本介绍

**fail-fast 机制是java集合(Collection)中的一种错误机制。**当多个线程对同一个集合的内容进行操作时，就可能会产生fail-fast事件。

例如：当某一个线程A通过iterator去遍历某集合的过程中，若该集合的内容被其他线程所改变了；那么线程A访问集合时，就会抛出**ConcurrentModificationException**异常，产生fail-fast事件。

## 2、解决方法

### 1）换并发包的类（推荐）

fail-fast机制，是一种错误检测机制。**它只能被用来检测错误，因为JDK并不保证fail-fast机制一定会发生。**

若在多线程环境下使用fail-fast机制的集合，建议**使用“java.util.concurrent包下的类”去取代“java.util包下的类”**。

### 2）加锁（不推荐）

在遍历过程中所有涉及到改变modCount值得地方全部加上synchronized或者直接使用Collections.synchronizedList，这样就可以解决。但是不推荐，因为增删造成的同步锁可能会阻塞遍历操作。



# 二、底层原理

## 1、ConcurrentModificationException产生原因

我们知道，ConcurrentModificationException是在操作Iterator时抛出的异常。我们先看看ArrayList的Iterator源码。

我们可以发现在调用 next() 和 remove()时，都会执行 **checkForComodification()**。若 “**modCount 不等于 expectedModCount**”，则抛出ConcurrentModificationException异常，产生fail-fast事件。

## 2、什么时候modCount 不等于 expectedModCount？

通过阅读ArrayList源码，我们发现：无论是add()、remove()，还是clear()，只要涉及到修改集合中的元素个数时，都会改变modCount的值。

所以，当多个线程对同一个集合进行操作的时候，某线程访问集合的过程中，该集合的内容被其他线程所改变(即其它线程通过add、remove、clear等方法，改变了modCount的值)；这时，就会抛出ConcurrentModificationException异常，产生fail-fast事件。

## 3、 解决fail-fast的原理

看看CopyOnWriteArrayList的源码：

从中，我们可以看出:

-  和ArrayList继承于AbstractList不同，CopyOnWriteArrayList没有继承于AbstractList，它仅仅只是实现了List接口。
- ArrayList的iterator()函数返回的Iterator是在AbstractList中实现的；而CopyOnWriteArrayList是自己实现Iterator。
- ArrayList的Iterator实现类中调用next()时，会“调用checkForComodification()比较‘expectedModCount’和‘modCount’的大小”；但是，CopyOnWriteArrayList的Iterator实现类中，没有所谓的checkForComodification()，更不会抛出ConcurrentModificationException异常！ 





## 参考文章

[Java 集合系列04之 fail-fast总结(通过ArrayList来说明fail-fast的原理、解决办法)](https://www.cnblogs.com/skywang12345/p/3308762.html)









