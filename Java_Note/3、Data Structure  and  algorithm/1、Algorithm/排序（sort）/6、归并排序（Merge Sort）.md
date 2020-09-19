[TOC]

# 归并排序（Merge Sort）

##  一、基本介绍

归并排序（MERGE-SORT）是利用归并的思想实现的排序方法，该算法采用经典的分治策略（分治法将问题分成一些小的问题然后递归求解，而治的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之)。

## 二、基本思想:

### 1、算法思路

- `分解` -- 将当前区间一分为二，即求分裂点 mid = (low + high)/2;

- `求解` -- 递归地对两个子区间a[low...mid] 和 a[mid+1...high]进行归并排序。递归的终结条件是子区间长度为1。

- `合并` -- 将已排序的两个子区间a[low...mid]和 a[mid+1...high]归并为一个有序的区间a[low...high]。




合并的思想：拷贝一个辅助数组，在辅助数组中进行比较，把最小的依次返回到原数组

### 2、思路图

![image-20200514221903348](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181727.png)



## 三、代码实现

### 1、归并01

```java
private void mergeSort(int[] nums, int left, int right, int[] temp) {
    if (left == right) {
        return 0;
    }

    int mid = left + (right - left) / 2;
    mergeSort(nums, left, mid, temp);
    mergeSort(nums, mid + 1, right, temp);

    if (nums[mid] <= nums[mid + 1]) {
        return leftPairs + rightPairs;
    }

    mergeTwoSortedArray(nums, left, mid, right, temp);

}

/**
 * 把两个已经排好序的数组进行合并
 * 第 1 个数组：arr[left, ..., mid]，是排好序的
 * 第 2 个数组：arr[mid+1, ..., right]，是排好序的
 *
 * @param arr   待排序数组
 * @param left  arr[left,mid] 已经是排好序的
 * @param mid
 * @param right arr[mid+1,right] 已经是排好序的
 */
private void mergeTwoSortedArray(int[] arr, int left, int mid, int right) {
    
    // 首先计算出这个数组的长度
    int length = right - left + 1;
    // 新建一个数组，赋值，用于比较
    // 这里每进行一次比较，都要 new 一个数组，开销很大
    int[] temp = new int[length];
    // 为新数组赋值
    for (int i = 0; i < length; i++) {
        temp[i] = arr[left + i];
    }
    // 左边数组的起始位置
    int l = 0;
    // 右边数组的起始位置
    int r = mid - left + 1;
    
    // 循环遍历把 arr 在 [left, right] 这个区间重新赋值
    // temp 数组中的元素不动，只是拿来做比较，然后我们一直修改的是 arr 数组在 [left, right] 的值
    for (int i = 0; i < length; i++) {
        // 先考虑如果左边数组用完（越界），把右边剩下的复制进排序数组中
        if (l > mid - left) {
            // 此时 l 遍历完成，就去拼命遍历 r 就好了
            arr[i + left] = temp[r];
            r++;
        } else if (r > length - 1) {  //如果右边数组用完了，把左边剩下的复制进排序数组中
            // 此时 r 遍历完成，就去拼命遍历 l 就好了
            arr[i + left] = temp[l];
            l++;
        } else if (temp[l] <= temp[r]) { //进行比较取最小的，<=保证稳定排序
            arr[i + left] = temp[l];
            l++;
        } else {
            arr[i + left] = temp[r];
            r++;
        }
    }
}
```

### 2、归并02

```java
   public static void mergeSort(int[] arr, int low, int high) {
        int mid = (low + high) / 2;
        if (low < high) {
            // 左边
            mergeSort(arr, low, mid);
            // 右边
            mergeSort(arr, mid + 1, high);
            // 左右归并
            merge(arr, low, mid, high);
            System.out.println(Arrays.toString(arr));
        }
    }

    // 将两个数组进行归并，归并前面2个数组已有序，归并后依然有序
        public static void merge(int[] arr, int low, int mid, int high) {
            int[] temp = new int[high - low + 1];
            int left = low;// 左指针
            int right = mid + 1;// 右指针
            int i = 0;
            // 从两个数组中取出较小的放入临时数组
            while (left <= mid && right <= high) {
                //<=保证稳定排序
                if (arr[left] <= arr[right]) {
                    temp[i++] = arr[left++];
                } else {
                    temp[i++] = arr[right++];
                }
            }
            // 剩余部分依次放入临时数组（实际上两个while只会执行其中一个）
            while (left <= mid) {
                temp[i++] = arr[left++];
            }
            while (right <= high) {
                temp[i++] = arr[right++];
            }

            // 将临时数组中的内容拷贝回原数组中 （left-right范围的内容）
            for (int k = 0; k < temp.length; k++) {
                arr[k + low] = temp[k];
            }
        }
```

### 优化的方向

优化 1：在「小区间」里转向使用「插入排序」，Java 源码里面也有类似这种操作，「小区间」的长度是个超参数，需要测试决定，我这里参考了 JDK 源码；

优化 2： 在「两个数组」本身就是有序的情况下，无需合并；



优化 3：全程使用一份临时数组进行「合并两个有序数组」的操作，避免创建临时数组和销毁的消耗，避免计算下标偏移量。

注意：实现归并排序的时候，要特别注意：在进行左右数组比较大小时，不要把这个算法实现成非稳定排序，区别就在 `<=` 和 `<` ，已在代码中注明。



## 四、优化

```java
public class Solution {

    public static void mergeSort(int[] arr, int low, int high) {
        int mid = (low + high) / 2;
        //优化1：插入排序
        if(high - low < 7){
            insertSort(arr, low, high);
            return;
        }
        if (low < high) {
            // 左边
            mergeSort(arr, low, mid);
            // 右边
            mergeSort(arr, mid + 1, high);
            //优化2：
            if (arr[mid] <= arr[mid + 1])
                return;

            // 左右归并
            merge(arr, low, mid, high);
            }

        }


    // 将两个数组进行归并，归并前面2个数组已有序，归并后依然有序
    public static void merge(int[] arr, int low, int mid, int high) {
        int[] temp = new int[high - low + 1];
        int left = low;// 左指针
        int right = mid + 1;// 右指针
        int i = 0;
        // 从两个数组中取出较小的放入临时数组
        while (left <= mid && right <= high) {
            if (arr[left] < arr[right]) {
                temp[i++] = arr[left++];
            } else {
                temp[i++] = arr[right++];
            }
        }
        // 剩余部分依次放入临时数组（实际上两个while只会执行其中一个）
        while (left <= mid) {
            temp[i++] = arr[left++];
        }
        while (right <= high) {
            temp[i++] = arr[right++];
        }

        // 将临时数组中的内容拷贝回原数组中 （left-right范围的内容）
        for (int k = 0; k < temp.length; k++) {
            arr[k + low] = temp[k];
        }
    }
    
    //插入排序
    private static void insertSort(int[] arr, int low, int high) {
        int i, j, temp = 0;
        for (i = low + 1; i <= high; i++) {
            if(arr[i]<arr[i-1]){
                temp = arr[i];
                for(j = i ; j > 0 && temp < arr[j-1] ; j --)
                    arr[j ] = arr[j-1];
                arr[j] = temp;
            }
        }
    }

	//测试
    public static void main(String[] args) {
        int[] arr = new int[10000000];
        for(int i = 0;i<10000000;i++){
            arr[i] = (int) (Math.random()*10000000);
        }
        mergeSort(arr,0,arr.length-1);
    }

}


```









## 五、工作原理

1、申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列

2、设定两个指针，最初位置分别为两个已经排序序列的起始位置

3、比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置

4、重复步骤3直到某一指针达到序列尾

5、将另一序列剩下的所有元素直接复制到合并序列尾







