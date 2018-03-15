---
title: IDEA中Spring boot热部署 Thymeleaf警告
date: 2018-03-15 22:39:41
tags: [IDEA,Spring Boot,Thymeleaf]
categories: IDEA
---

### 解决idea中Thymeleaf页面警告问题

#### maven module project项目会出现这个问题
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118004859_1.png)
<!--more-->
#### 解决方式在idea的官网也给出了答案，请点这个[进入官网](https://www.jetbrains.com/help/idea/2016.3/thymeleaf.html#d397188e121)
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118089581_1.jpg)

#### 按照官网的办法，我们进行一下设置，如下图：
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118132850_1.jpg)

### idea spring boot热部署问题
**如果不重启服务，修改html文件是不生效的，这样我们开发效率会很低啊，所以我们要想办法让web容器像原来那样，可以进行自动刷新生效。**
#### 首先需要在pom.xml文件中引入devtools包
```
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
```

#### 对idea进行一些配置:
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118240583_1.jpg)

#### 设置一下这个快捷键
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118276468_1.jpg)

#### 利用刚刚设置好的快捷键打开 Registry窗口，在蓝色的地方打钩
![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512118305304_1.jpg)
