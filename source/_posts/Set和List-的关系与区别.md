---
title: Set和List 的关系与区别
date: 2018-03-15 22:49:54
tags: [Java,Set,List]
categories: Java
---

**两个接口都是继承自Collection**



<!--more-->

- List (inteface)

  **次序是List 的最重要特点,它确保维护元素特定的顺序.**

  - ArrayList：允许对元素快速随机访问。
  - LinkedList：对顺序访问进行优化，向List中间插入与移除的开销并不大，具有addFrist()，addLast()，getFirst，getLast，removeFirst和removeLast()。这些方法使得LinkedList可当作堆栈／队列／双向队列。

- Set (inteface)

  **存入Set 的每个元素必须唯一，不保证维护元素的次序.加入Set 的Object必须定义equals()方法**

  - HashSet：为快速查找而设计的Set，存入HashSet对象必须定义hashCode()。
  - TreeSet：保护次序的Set，使用它可以从Set 中提取有序序列。
  - LinkedHashSet：具有HashSet的查询速度，且内部使用链表维护元素的次序。

**它们之间的存储方式不一样：**

- TreeSet采用红黑树的树据结构排序元素；


- HashSet采用散列函数，这是专门为快速查询而设计的；
- LinkedHashSet内部使用散列以加快查询速度，同时使用链表维护元素的次序。

