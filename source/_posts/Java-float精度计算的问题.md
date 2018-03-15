---
title: Java float精度计算的问题
date: 2018-03-14 17:59:24
tags: [java,float]
categories: Java
---

- 方法一
```java
float a = 123.2334f;
//(这里的100就是2位小数点,如果要其它位,如4位,这里两个100改成10000)
float b = (float) (Math.round(a * 100)) / 100;
```
- 方法二
<!--more-->
```java
String a = new DecimalFormat("###,###,###.##").format(100.12345);
```
- 方法三
```java
float ft = 134.3435f;
//设置位数
int scale = 2;
//表示四舍五入，可以选择其他舍值方式，例如去尾，等等.
int roundingMode = 4;
BigDecimal bd = new BigDecimal((double) ft);
bd = bd.setScale(scale, roundingMode);
ft = bd.floatValue();
```

