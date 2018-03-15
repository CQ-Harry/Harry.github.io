---
title: Java BigDecimal 小数点处理 计算
date: 2018-03-15 22:37:35
tags: [Java,BigDecimal]
categories: Java
---

#### 四舍五入
``` java
BigDecimal b1 = new BigDecimal(3.1415726);
b1 = b1.setScale(2, BigDecimal.ROUND_HALF_UP);
System.out.println(b1);
//输出：3.14
```

<!--more-->

#### 加法运算
```
BigDecimal b1 = new BigDecimal(3);
BigDecimal b2 = new BigDecimal(2);
System.out.println(b1.add(b2));
//输出：5
```

#### 减法运算
```
BigDecimal b1 = new BigDecimal(3);
BigDecimal b2 = new BigDecimal(1);
System.out.println(b1.subtract(b2));
//输出：2
```

#### 乘法运算
```
BigDecimal b1 = new BigDecimal(3);
BigDecimal b2 = new BigDecimal(2);
System.out.println(b1.multiply(b2));
// 输出：6
```

#### 除法运算
```
//当发生除不尽的情况时，精确到小数点以后10位，以后的数字四舍五入
BigDecimal b1 = new BigDecimal(13);
BigDecimal b2 = new BigDecimal(3);
System.out.println(b1.divide(b2, 10, BigDecimal.ROUND_HALF_UP));
// 输出：4.3333333333
```
