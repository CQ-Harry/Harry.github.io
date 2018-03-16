---
title: Spring Cloud 微服务实战（一）
date: 2018-03-15 23:07:28
tags: [Spring Cloud,微服务,actuator]
categories: Spring Cloud
---

# SpringBoot-actuator

> 该模块能够自动为Spring Boot 构建的应用提供一系列用于监控的端点。同时，Spring Cloud 在实现各个微服务组件的时候，进一步为该模块做了不少扩展，比如，为原生端点增加了更多的指标和度量信息。

<!--more-->
## 1.引入
> 在pom.xml的dependencies节点中新增spring-boot-starter-actuator的依赖即可：
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```
## 2.application.properties添加配置参数
> 访问/beans 等敏感的信息时候报错401
```properties
management.security.enabled=false
```
## 3.端点解析
> 分为3大类：应用配置类（autoconfig、beans、configprops、env、mappings、info）、度量指标类（metrics、health、dump、trace）、操作控制类（shutdown）

| ID  | 描述  | 敏感  |
| :------------ | :------------ | :------------ |
| autoconfig | 获取应用的自动化配置报告，其中包括所有自动化配置的候选项。同事还列出了每个候选项是否满足自动化配置的各个先决条件。所以，该端点可以帮助我们方便地找到一些自动化配置为什么没有生效的具体原因。该报告内容将自动化配置内容分为两部分：positiveMatches中返回的是条件匹配成功的自动化配置、negativeMatches中返回的是条件匹配不成功的自动化配置  | true  |
| beans | 获取应用上下文中创建的所有 Bean，每个 Bean 中包含：bean（Bean的名称）、scope（Bean的作用域）、type（Bean的Java类型）、resource（class文件的具体路径）、dependencies（依赖的Bean名称）  | true  |
| configprops | 获取应用中配置的属性信息报告（关闭该端点，使用endpoints.configprops.enabled=false来完成设置） | true  |
| env | 获取应用所有可用的环境属性报告。包括环境变量、JVM属性、应用的配置属性、命令行中参数。为了配置属性的安全，对于一些类似密码等敏感信息，该端点都会进行隐私保护，但是需要让属性名中包含password、secret、key这些关键字，这样该端点在返回它们的时候会使用*来替代实际的属性值。  | true  |
| mappings | 返回所有Spring MVC的控制器映射关系报告。其中bean属性标识了该映射关系的请求处理器，method属性标识了该映射关系的具体处理类和处理函数。 | true  |
| info | 返回一些应用自定义的信息。默认该端点只会返回一个空的JSON内容。可以在application.properties配置文件通过info前缀来设置一些属性。  | false |
| metrics | 返回当前应用的各类重要度量指标，比如内存信息、线程信息、垃圾回收信息等。还可以通过/metrics/{name}接口来更细粒度地获取度量信息。 | false |
| health | 获取应用的各类健康指标信息。 | true  |
| dump | 用来暴露程序运行中的线程信息。 | true  |
| trace | 返回基本的HTTP跟踪信息。默认跟踪信息的存储采用org.springframework.boot.actuate.trace.InMemoryTraceRepository实现的内存方式，始终保留最近的100条请求记录。 | true  |
| shutdown | 实现关闭该应用的远程操作。通过配置来开启：endpoints.shutdown.enabled=true | true  |
