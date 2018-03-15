---
title: IDEA控制台中文乱码解决方案
date: 2018-03-15 18:10:23
tags: [IDEA,乱码]
categories: IDEA
---


1. 打开Intellij的根目录，找到下图的两个文件（根据你的系统是32位或64位选择其中一个配置文件），在配置文件中添加：
```
-Dfile.encoding=UTF-8
```

<!--more-->

![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512117071813_1.png)

2. 配置项目编码以及IDE编码：
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512117128404_1.png)

3. 配置项目启动服务器参数：
```
-Dfile.encoding=UTF-8
```
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512117195505_1.png)