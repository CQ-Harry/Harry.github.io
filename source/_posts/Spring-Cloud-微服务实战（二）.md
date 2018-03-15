---
title: Spring Cloud 微服务实战（二）
date: 2018-03-15 23:10:41
tags: [Spring Cloud,微服务,Eureka]
categories: Spring Cloud
---

# 服务治理：Spring Cloud Eureka
> 该模块主要负责完成微服务架构中的服务治理功能。主要用来实现各个微服务实例的自动化注册于发现。
- 服务注册：
在服务治理框架中，通常都会构建一个注册中心，每个服务单元想注册中心登记自己提供的服务，将主机与端口号、版本号、通信协议等一些附加信息告知注册中心，注册中心按服务名分类组织服务清单。
- 服务发现：由于在服务治理框架下运作，服务间的调用不再通过指定具体的实例地址来实现，而是通过向服务名发起调用实现。所以，服务调用方在调用服务提供方接口的时候，并不知道具体的服务实例位置。因此，调用方需要向服务注册中心咨询服务，并获取所有服务的实例清单，以实现对具体服务实例的访问。

<!--more-->
## 搭建服务注册中心
> 新建Spring Boot项目，pom.xml中引入必要的依赖：
```xml
<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
</parent>
<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka-server</artifactId>
		</dependency>
</dependencies>
<dependencyManagement>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-dependencies</artifactId>
			<version>Dalston.SR3</version>
			<type>pom</type>
			<scope>import</scope>
		</dependency>
	</dependencies>
</dependencyManagement>
```
通过@EnableEurekaClient注解启动一个服务注册中心提供给其他应用进行对话。
```java
@EnableEurekaClient
@SpringBootApplication
public class Springclouddemo2Application {
		public static void main(String[] args) {
			new SpringApplicationBuilder(Application.class).web(true).run(args);
		}
}
```
application.properties添加配置：
```xml
eureka.instance.hostname=localhost
# 不向注册中心注册自己
eureka.client.register-with-eureka=false
# 由于注册中心的职责就是维护服务实例，不需要去检索服务
eureka.client.fetch-registry=false
eureka.client.serviceUrl.defaultZone=http://${eureka.instance.hostname}:${server.port}/eureka/
```
启动并访问http://localhost:1111/可以看到Eureka信息面板。

## 注册服务提供者
> 新建Spring Boot项目，pom.xml中引入必要的依赖：
```xml
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
</dependencies>
<dependencyManagement>
		<dependencies>
			<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-dependencies</artifactId>
			<version>Dalston.SR3</version>
			<type>pom</type>
			<scope>import</scope>
			</dependency>
		</dependencies>
</dependencyManagement>
```
新增HelloController
```java
@RestController
public class HelloController {
		private final Logger logger = Logger.getLogger(getClass());
		@Autowired
		private DiscoveryClient discoveryClient;
		@RequestMapping("/hello")
		public String index() {
			ServiceInstance instance = discoveryClient.getLocalServiceInstance();
			logger.info("/hello,host:" + instance.getHost() + ",service_id:" + instance.getServiceId());
			return "Hello World";
		}
}
```
在主类中通过加上@EnableEurekaClient注解，激活Eureka中的DiscoveryClient实现。
```java
@EnableEurekaClient
@SpringBootApplication
public class Springclouddemo2Application {
		public static void main(String[] args) {
			SpringApplication.run(Springclouddemo2Application.class, args);
		}
}
```
application.properties添加配置：
```xml
spring.application.name=hello-service
eureka.client.serviceUrl.defaultZone=http://localhost:1111/eureka/
```
**分别启动1.2.项目，在2项目的控制台com.netflix.discovery.DiscouveryClient对象打印了该服务的注册信息，表示注册成功。而此时在1项目控制台输出：名为hello-service的服务被注册成功了。**

## 高可用注册中心
> Eureka Server的高可用实际上就是将自己作为服务项其他服务注册中心注册自己，这样就可以形成一组相互注册的服务注册中心，以实现服务清单的互相同步，达到高可用的效果。在项目1基础上进行扩展，构建一个双节点的服务注册中心集群。
**·创建application-peer1.properties，作为peer1服务中心的配置，并将serviceUrl指向peer2：**
```xml
spring.application.name=eureka-server
server.port=1111
eureka.instance.hostname=peer1
eureka.client.serviceUrl.defaultZone=http://peer2:1112/eureka/
```
**·创建application-peer2.properties，作为peer2服务中心的配置，并将serviceUrl指向peer1：**
```xml
spring.application.name=eureka-server
server.port=1112
eureka.instance.hostname=peer2
eureka.client.serviceUrl.defaultZone=http://peer1:1111/eureka/
```
**·在hosts文件中添加对peer1和peer2的转换，让上面配置的host形式的serviceUrl能在本地正确访问到；**
```
127.0.0.1	peer1
127.0.0.1	peer2
```
**·通过spring.profiles.active属性来分别启动peer1和peer2：**
```
java -jar eureka-server-1.0.0.jar --spring.profiles.active=peer1
java -jar eureka-server-1.0.0.jar --spring.profiles.active=peer2
```
此时访问peer1的注册中心http://localhost:1111/ ，可以看到registered-replicas中已经有peer2节点的eureka-server了。同样的，访问peer2的注册中心http://localhost:1112/ ，也能看到registered-replicas中已经有peer1节点。
**·在设置了多节点的服务注册中心之后，服务提供方还需要配置才能将服务注册到Eureka Server集群中。修改项目2的application.properties配置文件：**
```xml
spring.application.name=hello-service
eureka.client.serviceUrl.defaultZone=http://peer1:1111/eureka/,http://peer2:1112/eureka/
```
启动该项目，访问http://localhost:1111/ 和 http://localhost:1112/ ,可以看到hello-server服务同时注册到peer1和peer2上。若此时断开peer1，由于同时也像peer2注册，因此在peer2上的服务依然能访问到hello-server，从而实现了服务注册中心的高可用。

## 服务发现与消费
> 前3点已经有了服务注册中心和服务提供者，现在构建一个服务消费者，主要完成两个目标，发现服务以及消费服务。服务发现的任务由Eureka的客户端完成，服务消费的任务由Ribbon完成。
**·启动项目2：hello-service服务，实验Ribbon的客户端负载均衡功能，使用java -jar命令行的方式来启动两个不同端口的hello-service，如下：**
```bash
java -jar hello-service-0.0.1-SNAPSHOT.jar --server.port=8081
java -jar hello-service-0.0.1-SNAPSHOT.jar --server.port=8082
```
**·启动成功后，从Eureka信息面板可以看到名为HELLO-SERVICE的服务中出现了两个实例单元，分别是通过8081端口和8082端口的服务。**
**·创建Spring Boot的基础工程来实现服务消费者（ribbon-consumer），在pom.xml引入如下依赖：**
```xml
<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-eureka</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-ribbon</artifactId>
		</dependency>
	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Dalston.SR3</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
```
**·在应用启动主类通过@EnableDiscoveryClient注解让该应用注册为Eureka客户端应用，以获得服务发现的能力。同时在该主类中创建RestTemplate的Spring Bean实例，并通过@LoadBalanced注解开启客户端负载均衡。**
```java
@EnableDiscoveryClient
@SpringBootApplication
public class RibbonConsumerApplication {
		@Bean
		@LoadBalanced
		RestTemplate restTemplate(){
			return new RestTemplate();
		}
		public static void main(String[] args) {
			SpringApplication.run(RibbonConsumerApplication.class, args);
		}
}
```
**·创建ConsumerController类并实现/ribbon-consumer接口。在该接口中，通过在上面创建的RestTemplate来实现对HELLO-SERVICE服务提供的/hello接口进行调用。**
```java
@RestController
public class ConsumerController {
		@Autowired
		RestTemplate restTemplate;
		@RequestMapping(value = "/ribbon-consumer", method = RequestMethod.GET)
		public String helloConsumer() {
			return restTemplate.getForEntity("http://HELLO-SERVICE/hello", String.class).getBody();
		}
}
```
**·在application.properties中配置Eureka服务注册中心的位置，需要与之前的HELLO-SERVER一样，不然是发现不了该服务的，同时设置该消费者的端口为9000，否则会冲突。**
```xml
spring.application.name=ribbon-consumer
server.port=9000
eureka.client.serviceUrl.defaultZone=http://localhost:1111/eureka/
```
**·启动ribbon-consumer应用后，在Eureka信息面板中看到除了HELLO-SERVER之外，还有RIBBON-CONSUMER服务。**
**·访问http：//localhost:9000/ribbon-consumer 发起GET请求，返回“Hello World”。在ribbon-consumer控制台看到Ribbon输出了当前客户端维护的HELLO-SERVICE的服务列表情况。**
