# ReadWriteLock

ReentrantReadWriteLock是ReadWriteLock的一个实现类

```
public class ReentrantReadWriteLock
        implements ReadWriteLock, java.io.Serializable {
```

使用`ReadWriteLock`可以解决这个问题，它保证：

- 只允许一个线程写入（其他线程既不能写入也不能读取）；
- 没有写入时，多个线程允许同时读（提高性能）。