#  归并排序（Merge Sort）

------

 

[TOC]



##  一、基本介绍

归并排序（MERGE-SORT）是利用归并的思想实现的排序方法，该算法采用经典的分治策略（分治法将问题分成一些小的问题然后递归求解，而治的阶段则将分的阶段得到的各答案"修补"在一起，即分而治之)。

## 二、基本思想:

### 1、算法思路

- `分解` -- 将当前区间一分为二，即求分裂点 mid = (low + high)/2;

- `求解` -- 递归地对两个子区间a[low...mid] 和 a[mid+1...high]进行归并排序。递归的终结条件是子区间长度为1。

- `合并` -- 将已排序的两个子区间a[low...mid]和 a[mid+1...high]归并为一个有序的区间a[low...high]。

  

### 2、思路图

![image-20200514221903348](E:\black user\Java\有道云截图\image-20200514221903348.png)



## 三、代码实现

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
```



## 四、工作原理

1、申请空间，使其大小为两个已经排序序列之和，该空间用来存放合并后的序列

2、设定两个指针，最初位置分别为两个已经排序序列的起始位置

3、比较两个指针所指向的元素，选择相对小的元素放入到合并空间，并移动指针到下一位置

4、重复步骤3直到某一指针达到序列尾

5、将另一序列剩下的所有元素直接复制到合并序列尾



