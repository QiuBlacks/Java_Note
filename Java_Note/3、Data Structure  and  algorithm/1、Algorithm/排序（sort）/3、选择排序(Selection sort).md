# 选择排序(Selection sort)

 

## **一、基本介绍**

 

选择式排序也属于内部排序法，是从欲排序的数据中，按指定的规则选出某一元素，再依规定交换位置后达到排序的目的。

## **二、选择排序**

### 1、基本思想

在要排序的一组数中，选出最小的一个数与第一个位置的数交换；然后在剩下的数当中再找最小的与第二个位置的数交换，如此循环到倒数第二个数和最后一个数比较为止

(每一趟从待排序序列选择一个最小的元素放到已排好序序列的末尾，剩下的为待排序序列，重复上述步骤直到完成排序。)

### 2、代码实现

```java
public void selectSort(int array[]) {
		int t = 0;
		for (int i = 0; i < array.length - 1; i++){
			int min=i;
			for (int j = i + 1; j < array.length; j++)
				if (array[min] > array[j])
					minmin=j;
			if(min!=i){ //找到了比array[i]小的则与array[i]交换位置
				t = array[i];
				array[i] = array[min];
				array[min] = t;
			}
		}
	}
```



