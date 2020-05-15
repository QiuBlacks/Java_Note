#  **插入排序** （Insertion  Sort）

 

## 一、插入排序法介绍:

 

插入式排序属于内部排序法，是对于欲排序的元素以插入的方式找寻该元素的适当位置，以达到排序的目的。

 

## 二、插入排序法

###  1、基本思想

每步将一个待排序的记录，按其顺序码大小插入到前面已经排序的字序列的合适位置（从后向前找到合适位置后），直到全部插入排序完为止。

 

### 2、思路图:

![img](E:\black user\Java\有道云截图\nU3MJrY-1589361939325.gif)



### 3、代码实现

```java
public void insertionSort(int array[]) {
		int i, j, temp = 0;
		for (i = 1; i < array.length; i++) {
			if(array[i]<array[i-1]){
				temp = array[i];
                for(j = i ; j > 0 && temp < numbers[j-1] ; j --)
					array[j ] = array[j-1];
				array[j] = temp;
			}
		}
}
```





## **四、 算法效率**

直接插入排序不是稳定的排序算法。

| 平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度 |
| -------------- | -------- | -------- | ---------- |
| O(n^ 2 )       | O(n)     | O(n^ 2 ) | O(1)       |