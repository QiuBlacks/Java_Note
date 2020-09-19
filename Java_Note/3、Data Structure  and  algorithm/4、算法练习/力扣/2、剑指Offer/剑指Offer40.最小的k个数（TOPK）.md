# 剑指Offer40.最小的k个数（TOPK）

## 一、题目介绍

输入整数数组 `arr` ，找出其中最小的 `k` 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

## 二、解决方法（去看算法思路topK）

### 1、快排

#### 1）思想

- 若 k = m，我们就找到了最小的 kk 个数，就是左侧的数组；
- 若 k<m，则最小的 kk 个数一定都在左侧数组中，我们只需要对左侧数组递归地 parition 即可；
- 若 k>m，则左侧数组中的 mm 个数都属于最小的 kk 个数，我们还需要在右侧数组中寻找最小的 k-mk−m 个数，对右侧数组递归地 partition 即可



```java
public int[] getLeastNumbers(int[] arr, int k) {
    if (k == 0) {
        return new int[0];
    } else if (arr.length <= k) {
        return arr;
    }
    
    // 原地不断划分数组
    partitionArray(arr, 0, arr.length - 1, k);
    
    // 数组的前 k 个数此时就是最小的 k 个数，将其存入结果
    int[] res = new int[k];
    for (int i = 0; i < k; i++) {
        res[i] = arr[i];
    }
    return res;
}

void partitionArray(int[] arr, int lo, int hi, int k) {
    // 做一次 partition 操作
    int m = partition(arr, lo, hi);
    // 此时数组前 m 个数，就是最小的 m 个数
    if (k == m) {
        // 正好找到最小的 k(m) 个数
        return;
    } else if (k < m) {
        // 最小的 k 个数一定在前 m 个数中，递归划分
        partitionArray(arr, lo, m-1, k);
    } else {
        // 在右侧数组中寻找最小的 k-m 个数，
        //注意：k是即数组的第k个数，原因与m比较，是看m的取值，partition函数return的是数组下标
        partitionArray(arr, m+1, hi, k);
    }
}

// partition 函数和快速排序中相同，具体可参考快速排序相关的资料
// 代码参考 Sedgewick 的《算法4》
int partition(int[] a, int lo, int hi) {
    int i = lo;
    int j = hi + 1;
    int v = a[lo];
    while (true) { 
        while (a[++i] < v) {
            if (i == hi) {
                break;
            }
        }
        while (a[--j] > v) {
            if (j == lo) {
                break;
            }
        }

        if (i >= j) {
            break;
        }
        swap(a, i, j);
    }
    swap(a, lo, j);

    // a[lo .. j-1] <= a[j] <= a[j+1 .. hi]
    return j;
}

void swap(int[] a, int i, int j) {
    int temp = a[i];
    a[i] = a[j];
    a[j] = temp;
}
```

### 2）sort()

当然，最简单的就是使用sort方法进行排序啦