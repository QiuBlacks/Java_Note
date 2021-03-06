# 冒泡排序（BubbleSorting）

## 一、简单冒泡

### 1、基本思想

通过对待排序序列从前向后（从下标较小的元素开始）,依次比较相邻元素的值，若发现逆序则交换，**每一趟将待排序序列中最大元素移到最后**，剩下的为新的待排序序列，重复上述步骤直到排完所有元素。  当然也可以从后往前排。

### 2、代码实现

```java
public static void bubbleSort1(int[] a, int n) {
        int i,j;

        for (i=n-1; i>0; i--) {
            // 将a[0...i]中最大的数据放在末尾
            for (j=0; j<i; j++) {

                if (a[j] > a[j+1]) {
                    // 交换a[j]和a[j+1]
                    int tmp = a[j];
                    a[j] = a[j+1];
                    a[j+1] = tmp;
                }
            }
        }
    }
```



## 二、优化：

### 1、基本思路

因为排序的过程中，各元素不断接近自己的位置，如果**一趟比较下来没有进行过交换，就说明序列有序**，因此要在排序过程中设置一个标志 flag判断元素是否进行过交换。从而减少不必要的比较。(这里说的优化，可以在冒泡排序写好后，在进行)

### 2、代码实现

```java
  public static void bubbleSort ( int[] arr){
            int temp = 0; // 临时变量
            boolean flag = false; // 标识变量，表示是否进行过交换
      
            for (int i = 0; i < arr.length - 1; i++) {
                for (int j = 0; j < arr.length - 1 - i; j++) {
                    // 如果前面的数比后面的数大，则交换
                    if (arr[j] > arr[j + 1]) {    
                        temp = arr[j];
                        arr[j] = arr[j + 1];
                        arr[j + 1] = temp;
                        flag = true;
                    }
                }

                if (!flag) { // 在一趟排序中，一次交换都没有发生过
                    break;
                } else {
                    flag = false; // 重置flag!!!, 进行下次判断
                }
                
            }
        }
    }
```



## 三、算法效率

| 平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度 |
| -------------- | -------- | -------- | ---------- |
| O(n ^2 )       | O(n)     | O(n ^2 ) | O(1)       |

