

==底层实现是TreeMap==





支持两种排序方式：自然排序和定制排序

通过compare或者comparaeTo函数来判断元素是否相等.

compare函数通过判断两个对象的id，相同的id判断为重复元素，不会被加入到集合中。