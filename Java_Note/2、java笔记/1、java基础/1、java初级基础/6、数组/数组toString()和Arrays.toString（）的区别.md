[TOC]



## 数组toString()和Arrays.toString（）的区别

---

## 一、实例介绍
```java
 public static void main(String[] args)  {
        Object o=new Object();
        int[] arr=new int[3];
        arr[0]=1;
        System.out.println(arr.toString());
        System.out.println(Arrays.toString(arr));
        System.out.println(o.toString());
        }
```
### 输出结果：
![image-20200608192722875](E:\black user\Java\有道云截图\image-20200608192722875.png)

### 结果解析：
如果数组直接使用toString()就返回：类型@哈希值  <br>
如果数组使用Arrays.toString(arr)就直接返回数组内容<br>
如果对象调用toString()则返回：全类名+@+16进制无符号hashcode字符串

## 二、底层代码详解

### 1、object.toString()
```
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }
```

### 2、Arrays.toString(long[] a):
```java
    public static String toString(long[] a) {
        if (a == null)
            return "null";
        int iMax = a.length - 1;
        if (iMax == -1)
            return "[]";

        StringBuilder b = new StringBuilder();
        b.append('[');
        for (int i = 0; ; i++) {
            b.append(a[i]);
            if (i == iMax)
                return b.append(']').toString();
            b.append(", ");
        }
    }
```
因为String没有重写toString(),而是重载，那么就依次显示：类名+地址

