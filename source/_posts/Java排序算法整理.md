---
title: Java排序算法整理
date: 2018-03-15 23:05:14
tags: [Java,排序算法]
categories: Java
---

#### 冒泡排序法：专门针对已部分排序的数据进行排序。如果在你的数据清单中只有一两个数据是乱序的，用这种算法就是最快的排序算法。如果你的数据清单中的数据是随机排列的，那么这种方法就有可能是最慢的算法。
<!--more-->
> 该算法的核心思想是扫描数据清单。寻找出现乱序的两个相邻的项目，当找到这两个项目后，交换项目的位置然后继续扫描。重复上面的操作直到所有的项目都按照顺序排好。
**举个栗子：**
```java
public static void bubbleSort() {
	int a[] = { 3, 6, 4, 2, 11, 10, 5 };
	int temp = 0;
	for (int i = 0; i < a.length - 1; i++) {
		for (int j = 0; j < a.length - 1 - i; j++) {
			if (a[j] > a[j + 1]) {
				temp = a[j];
				a[j] = a[j + 1];
				a[j + 1] = temp;
			}
		}
	}
	for (int i = 0; i < a.length; i++) {
		System.out.println(a[i]);
	}
}
```



**未完待续**
