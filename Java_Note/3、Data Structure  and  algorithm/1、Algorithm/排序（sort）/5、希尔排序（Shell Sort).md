# **希尔排序 （Shell Sort) -----缩小增量排序**

------

[TOC]



## **一、基本思想：**

先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，待整个序列中的记录“基本有序”时，再对全体记录进行依次直接插入排序。

希尔排序也是一种**插入排序**，它是简单插入排序经过改进之后的一个**更高效的版本**，也称为**缩小增量排序**

## **二、算法描述**

①. 选择一个增量（等差：差为gap）序列t1，t2，…， [tk ](http://www.liuhaihua.cn/archives/tag/tk)，其中ti>tj，tk=1；（一般初次取数组半长，之后每次再减半，直到增量为1）

②. 按增量序列个数k，对序列进行k 趟排序；

③. 每趟排序，根据对应的增量ti，将待排序列分割成若干长度为m 的子序列，分别对各子表进行直接插入排序。仅增量因子为1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

## 三、代码实现

### 		1、交换法

```java
    public static int[] sort(int[] arr){
        //初始化gap
        int gap = arr.length/2;
        while(gap > 0){
            //插入排序
            for(int i = gap; i < arr.length; i++){
                int j=i;
                while(j >= gap && arr[j-gap] > arr[j]){
                    int temp = arr[j-gap]+arr[j];
                    arr[j-gap] = temp-arr[j-gap];
                    arr[j] = temp-arr[j-gap];
                    j -= gap;
                }
            }
            gap = gap/2;
        }
        return arr;
    } 


public static void shellSort(int[] arr) {

        int temp = 0;
        // 设置步长gap大小
        for (int gap = arr.length / 2; gap > 0; gap /= 2) {
            //插入排序：
            //从第gap个元素，逐个对其所在组进行直接插入排序操作
            for (int i = gap; i < arr.length; i++) {
                // 遍历各组中所有的元素, 对前面的数进行逐个比较，步长gap
                for (int j = i - gap; j >= 0; j -= gap) {
                    // 如果当前元素大于加上步长后的那个元素，说明交换
                    if (arr[j] > arr[j + gap]) {
                        temp = arr[j];
                        arr[j] = arr[j + gap];
                        arr[j + gap] = temp;
                    }
                }
            }
        }
    }

```

### 		2、移位法（速度更快）

```java
  public static void shellSort2(int[] arr) {

        // 增量gap, 并逐步的缩小增量
        for (int gap = arr.length / 2; gap > 0; gap /= 2) {
            // 从第gap个元素，逐个对其所在的组进行直接插入排序
            for (int i = gap; i < arr.length; i++) {
                int j = i;
                int temp = arr[j];
                if (arr[j] < arr[j - gap]) {
                    while (j - gap >= 0 && temp < arr[j - gap]) {
                        //移动
                        arr[j] = arr[j-gap];
                        j -= gap;
                    }
                    //当退出while后，就给temp找到插入的位置
                    arr[j] = temp;
                }
            }
        }
    }
}
```

参考：https://blog.csdn.net/qq_39207948/article/details/80006224