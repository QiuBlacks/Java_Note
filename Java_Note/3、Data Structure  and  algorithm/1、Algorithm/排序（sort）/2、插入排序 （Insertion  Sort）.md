#  插入排序 （Insertion  Sort）

##  一、插入排序法介绍

插入式排序属于内部排序法，是对于欲排序的元素以插入的方式找寻该元素的适当位置，以达到排序的目的。

 遍历每个数，插入相应的位置

## 二、插入排序法

###  1、基本思想

每步将一个待排序的记录，按其顺序码大小插入到前面已经排序的字序列的合适位置（从后向前找到合适位置后），直到全部插入排序完为止。

 

### 2、思路图:

![img](https://gitee.com/BlacksJack/picture-bed/raw/master/img/20200910181710.gif)



### 3、代码实现

```java
public void insertionSort(int array[]) {
		int i, j, temp = 0;
		for (i = 1; i < array.length; i++) {
            //数组是有序的，如果后面的大于前面的，就不用排序
			if(array[i]<array[i-1]){
				temp = array[i];	
                //数组后移
                for(j = i ; j > 0 && temp < array[j-1] ; j--)
					array[j ] = array[j-1];
                //插入
				array[j] = temp;
			}
		}
}
```





## 四、 算法效率

直接插入排序不是稳定的排序算法。

| 平均时间复杂度 | 最好情况 | 最坏情况 | 空间复杂度 |
| -------------- | -------- | -------- | ---------- |
| O(n^ 2 )       | O(n)     | O(n^ 2 ) | O(1)       |