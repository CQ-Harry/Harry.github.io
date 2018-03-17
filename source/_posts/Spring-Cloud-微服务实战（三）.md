---
title: Spring Cloud 微服务实战（三）
date: 2018-03-17 12:00:38
tags: [Spring Cloud,微服务,Ribbon]
categories: Spring Cloud
---

### 客户端负载均衡：Spring Cloud Ribbon

> Spring Cloud Ribbon是一个基于HTTP和TCP的客户端负载均衡工具，它基于Netflix Ribbon实现。通过Spring Cloud 的封装，可以将面向服务的REST模板请求自动转化成客户端负载均衡的服务调用。Spring Cloud Ribbon虽然只是一个工具类框架，不像服务注册中心、配置中心、API网关需要独立部署，它几乎存在于每一个Spring Cloud 构建的微服务和基础设施中。因为微服务间的调用，API网关的请求转发等内容，实际上都是通过Ribbon实现。

<!--more-->

#### 1. 客户端负载均衡
> 负载均衡在系统架构中是一个非常重要，并且是不得不去实施的内容。因为负载均衡是对系统的高可用、网络压力的缓解和处理能力扩容的重要手段之一。常说的负载均衡指服务端负载均衡，分为硬件负载均衡(在服务器节点之间安装专门用于负载均衡的设备)和软件负载均衡（Nginx等）。
> 客户端负载均衡和服务端负载均衡最大的不同点：服务清单所存储的位置。客服端负载均衡中，所有客户端节点都维护着自己要访问的来自服务注册中心的服务端清单。
> 用Spring Boot Ribbon在微服务调用方法：①服务提供者启动多个服务实例并注册到一个注册中心或者多个相关联的服务注册中心、②服务消费者直接通过调用被@LoadBalanced注解修饰过的RestTemplate实现面向服务的接口调用。

#### 2. RestTemplate详解
##### 2.1 GET请求

```java
//返回的ResponseEntity对象中body返回String类型
ResponseEntity<String> responseEntity = restTemplate.getForEntity("http://USER-SERVICE/user?name={1}",String.class,"didi");
String body = responseEntity.getBody();
//返回的ResponseEntity对象中body返回User类型
ResponseEntity<User> responseEntity = restTemplate.getForEntity("http://USER-SERVICE/user?name={1}",User.class,"didi");
User body = responseEntity.getBody();
```
###### 2.1.1 getForEntity三种不同的重载实现：
- getForEntity(String url, Class<T> responseType, Object... uriVariables)

```java
restTemplate.getForEntity("http://USER-SERVICE/user?name={1}",String.class,"didi");
```
- getForEntity(String url, Class<T> responseType, Map<String, ?> uriVariables)

```java
Map<String,String> params = new HashMap<>();
params.put("name","didi");
ResponseEntity<String> responseEntity = restTemplate.getForEntity("http://USER-SERVICE/user?name={name}",String.class,params);
```
- getForEntity(URI url, Class<T> responseType)

```java
UriComponents uriComponents = UriComponentsBuilder.fromUriString("http://USER-SERVICE/user?name={name}").build().expand("dodo").encode();
URI uri = uriComponents.toUri();
restTemplate.getForEntity(uri,String.class);
```
###### 2.1.2 getForObject三种不同的重载实现：
```java
UriComponents uriComponents = UriComponentsBuilder.fromUriString("http://USER-SERVICE/user?name={name}").build().expand("dodo").encode();
URI uri = uriComponents.toUri();
//body是String
String uesult = restTemplate.getForObject(uri,String.class);
//body是一个User对象
User uesult = restTemplate.getForObject(uri,User.class);
```
- getForObject(String url, Class<T> responseType, Object... uriVariables)
- getForObject(String url, Class<T> responseType, Map<String, ?> uriVariables)
- getForObject(URI url, Class<T> responseType)

##### 2.2 POST请求

> 相比较GET请求新增request参数，可以是一个普通对象，也可以是一个HttpEntity对象。如果是一个普通对象，RestTemplate会将请求对象转换为一个HttpEntity对象来处理，request内容会被视为完整的body处理；而如果request是一个HttpEntity对象，就会被当作一个完整的HTTP请求处理，包含body内容、header内容。

```java
User user = new User("didi",30);
ResponseEntity<String> responseEntity = restTemplate.postForEntity("http://USER-SERVICE/user",user,String.class);
```
###### 2.2.1 getForEntity三种不同的重载实现：
- postForEntity(String url, Object request, Class<T> responseType, Object... uriVariables)
- postForEntity(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
- postForEntity(URI url, Object request, Class<T> responseType)

###### 2.2.2 getForObject三种不同的重载实现：
- postForObject(String url, Object request, Class<T> responseType, Object... uriVariables)
- postForObject(String url, Object request, Class<T> responseType, Map<String, ?> uriVariables)
- postForObject(URI url, Object request, Class<T> responseType)

###### 2.2.3 postForLocation三种不同的重载实现：
- postForLocation(String url, Object request, Object... uriVariables)
- postForLocation(String url, Object request, Map<String, ?> uriVariables)
- postForLocation(URI url, Object request)

##### 2.3 PUT请求

```java
Long id = 10001L;
User user = new User("didi",40);
restTemplate.put("http://USER-SERVICE/user/{1}",user,id);
```
**put三种不同的重载实现：**

- put(String url, Object request, Object... uriVariables)
- put(String url, Object request, Map<String, ?> uriVariables)
- put(URI url, Object request)

##### 2.4 DELETE请求

```java
Long id = 10001L;
restTemplate.delete("http://USER-SERVICE/user/{1}",id);
```
**delete三种不同的重载实现：**

- delete(String url, Object... uriVariables)
- delete(String url, Map<String, ?> uriVariables)
- delete(URI url)