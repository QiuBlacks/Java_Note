# BLOCKED、WAITING、TIMED_WAITING



## BLOCKED状态

线程处于BLOCKED状态的场景

- 当前线程在等待一个monitor lock，比如等待执行synchronized代码块或者使用synchronized标记的方法。
- 在synchronized块中循环调用Object类型的wait方法，如下是样例 synchronized(this) { while (flag) { obj.wait(); } // some other code }



## WAITING状态

线程处于WAITING状态的场景。

- 调用Object对象的wait方法，但没有指定超时值。
- 调用Thread对象的join方法，但没有指定超时值。
- 调用LockSupport对象的park方法。

提到WAITING状态，顺便提一下TIMED_WAITING状态的场景。



## TIMED_WAITING状态

线程处于TIMED_WAITING状态的场景。

- 调用Thread.sleep方法。
- 调用Object对象的wait方法，指定超时值。
- 调用Thread对象的join方法，指定超时值。
- 调用LockSupport对象的parkNanos方法。
- 调用LockSupport对象的parkUntil方法。