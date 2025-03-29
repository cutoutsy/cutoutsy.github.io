---
layout: post
title: 排序算法总结附代码(Java)
categories: 算法
date: 2018-08-16 10:43:17
tags:
    - 排序
    - 算法
    - Java
---
### 写在前面
本篇博客主要是对常见排序算法进行一个总结，每个排序算法附上Java代码实现。

<!-- more -->
### 冒泡排序
冒泡排序是最基础的排序，我们上数据结构与算法时学的第一个排序算法应该就是这个。冒泡排序，顾名思义，它排序的过程就像冒泡一样。

从数组的左边开始，每次进行两个数的比较，若第一个数比第二个数大，则进行交换，否则就不交换，这样直到数组末，那么最大值就会被放到最后一个位置上。

重复上面的过程，那么最后就会得到一个有序的数组。

时间复杂度：$O(n^2)$

#### 代码
```Java
public static void bubbleSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	for (int e = arr.length - 1; e > 0; e--) {
		for (int i = 0; i < e; i++) {
			if (arr[i] > arr[i + 1]) {
				swap(arr, i, i + 1);
			}
		}
	}
}

public static void swap(int[] arr, int i, int j) {
	arr[i] = arr[i] ^ arr[j];
	arr[j] = arr[i] ^ arr[j];
	arr[i] = arr[i] ^ arr[j];
}
```

### 插入排序
插入排序类似于我们平时玩扑克牌，假设我们现在手上的牌是有序的，那么新来一张牌那我们就需要去找把它插在合适的位置。

从数组的左边开始，假设当前下标i，那么你要依次和数组左边的值进行比较，如果左边的值大，则进行交换，直到交换到合适的位置。

重复上面的过程，最后会得到一个有序的数组。

时间复杂度：$O(n^2)$

#### 代码
```Java
public static void insertionSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	for (int i = 1; i < arr.length; i++) {
		for (int j = i - 1; j >= 0 && arr[j] > arr[j + 1]; j--) {
			swap(arr, j, j + 1);
		}
	}
}

public static void swap(int[] arr, int i, int j) {
	arr[i] = arr[i] ^ arr[j];
	arr[j] = arr[i] ^ arr[j];
	arr[i] = arr[i] ^ arr[j];
}
```

### 选择排序
选择排序的思想很简单，就是每次遍历选择一个最小值，然后与开头的数组的值进行交换。

时间复杂度：$O(n^2)$

#### 代码
```Java
public static void selectionSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	for (int i = 0; i < arr.length - 1; i++) {
		int minIndex = i;
		for (int j = i + 1; j < arr.length; j++) {
			minIndex = arr[j] < arr[minIndex] ? j : minIndex;
		}
		swap(arr, i, minIndex);
	}
}

public static void swap(int[] arr, int i, int j) {
	int tmp = arr[i];
	arr[i] = arr[j];
	arr[j] = tmp;
}
```

### 归并排序
归并排序运用了递归的思想。

给定一个数组，首先将数组均分成两部分，分别将左右部分排好序，然后再合并到一个数组中，所以一般来说归并排序会开辟O(n)的额外空间。递归地进行下去，就可以排序好数组。

时间复杂度：$O(nlogn)$

#### 代码
```Java
public static void mergeSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	mergeSort(arr, 0, arr.length - 1);
}

public static void mergeSort(int[] arr, int l, int r) {
	if (l == r) {
		return;
	}
	int mid = l + ((r - l) >> 1);
	mergeSort(arr, l, mid);
	mergeSort(arr, mid + 1, r);
	merge(arr, l, mid, r);
}

public static void merge(int[] arr, int l, int m, int r) {
	int[] help = new int[r - l + 1];
	int i = 0;
	int p1 = l;
	int p2 = m + 1;
	while (p1 <= m && p2 <= r) {
		help[i++] = arr[p1] < arr[p2] ? arr[p1++] : arr[p2++];
	}
	while (p1 <= m) {
		help[i++] = arr[p1++];
	}
	while (p2 <= r) {
		help[i++] = arr[p2++];
	}
	for (i = 0; i < help.length; i++) {
		arr[l + i] = help[i];
	}
}
```

### 快速排序
快速排序应该是现在工程上用得最多的排序算法了，好多库中实现的算法都包含了快速排序。

快速排序的思想是选取数组中的一个数作为基准，然后小于等于这个数的放在数组的左边，大于这个数的放在数组的右边，然后递归下去，直到数组有序。

时间复杂度：$O(nlogn)$

#### 代码
```Java
public static void quickSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	quickSort(arr, 0, arr.length - 1);
}

public static void quickSort(int[] arr, int l, int r) {
	if (l < r) {
		swap(arr, l + (int) (Math.random() * (r - l + 1)), r);
		int[] p = partition(arr, l, r);
		quickSort(arr, l, p[0] - 1);
		quickSort(arr, p[1] + 1, r);
	}
}

public static int[] partition(int[] arr, int l, int r) {
	int less = l - 1;
	int more = r;
	while (l < more) {
		if (arr[l] < arr[r]) {
			swap(arr, ++less, l++);
		} else if (arr[l] > arr[r]) {
			swap(arr, --more, l);
		} else {
			l++;
		}
	}
	swap(arr, more, r);
	return new int[] { less + 1, more };
}

public static void swap(int[] arr, int i, int j) {
	int tmp = arr[i];
	arr[i] = arr[j];
	arr[j] = tmp;
}
```

### 堆排序
堆排序是一种很重要的排序，其中堆结构需要重点掌握，在很多地方都会用到堆结构。

堆其实是一颗完全二叉树，不过是用数组来存储树结构的。

堆分为大顶堆和小顶堆，大顶堆就是二叉树的任何一个父结点的值都比其子结点的值要大，小顶堆就是任何一个父结点的值都比其子结点的值要小。

对于数组，怎么来表示一个值的父结点和叶子结点呢，是用数组的下标来表示的。比如一个结点在数组中的下标为i，那么它的左右子结点分别为2i+1、2i+2，其父结点为$\frac{i-1}{2}$。

堆排序是依次将数组中的每个值加入到堆结构中（每加入一个值就会进行堆调整），形成大根堆，然后将堆顶元素和数组最后一个元素互换，同时将堆大小减1，再进行堆调整，直到堆大小变为1。

时间复杂度：$O(nlogn)$

#### Java代码
```Java
public static void heapSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	for (int i = 0; i < arr.length; i++) {
		heapInsert(arr, i);
	}
	int size = arr.length;
	swap(arr, 0, --size);
	while (size > 0) {
		heapify(arr, 0, size);
		swap(arr, 0, --size);
	}
}

public static void heapInsert(int[] arr, int index) {
	while (arr[index] > arr[(index - 1) / 2]) {
		swap(arr, index, (index - 1) / 2);
		index = (index - 1) / 2;
	}
}

public static void heapify(int[] arr, int index, int size) {
	int left = index * 2 + 1;
	while (left < size) {
		int largest = left + 1 < size && arr[left + 1] > arr[left] ? left + 1 : left;
		largest = arr[largest] > arr[index] ? largest : index;
		if (largest == index) {
			break;
		}
		swap(arr, largest, index);
		index = largest;
		left = index * 2 + 1;
	}
}

public static void swap(int[] arr, int i, int j) {
	int tmp = arr[i];
	arr[i] = arr[j];
	arr[j] = tmp;
}
```

### 桶排序
桶排序思想比较简单，给定一个数组，先遍历找到这个数组的最大值max和最小值min，然后初始化桶的长度为max - min + 1，然后遍历数组中的数并将其依次放在桶的相应位置，然后再依次将这些值拷贝回原数组就会排好序了。

时间复杂度：$O(n)$

#### Java代码
```Java
public static void bucketSort(int[] arr) {
	if (arr == null || arr.length < 2) {
		return;
	}
	int max = Integer.MIN_VALUE;
	for (int i = 0; i < arr.length; i++) {
		max = Math.max(max, arr[i]);
	}
	int[] bucket = new int[max + 1];
	for (int i = 0; i < arr.length; i++) {
		bucket[arr[i]]++;
	}
	int i = 0;
	for (int j = 0; j < bucket.length; j++) {
		while (bucket[j]-- > 0) {
			arr[i++] = j;
		}
	}
}
```

### 排序算法的稳定性

### 排序算法的补充