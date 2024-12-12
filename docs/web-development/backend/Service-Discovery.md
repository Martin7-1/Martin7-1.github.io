---
title: 服务注册与服务发现
date: 2022-07-20 13:58:10
description: 服务注册与服务发现，Eureka服务中心简介
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku67.jpg
tags:
    - 微服务
    - SpringCloud
categories:
    - Cloud Native
    - 微服务
    - SpringCloud
---

## 服务拆分

**服务拆分的注意事项**

> 根据功能模块进行服务拆分，随着扩展可能会继续拆分

<!-- more -->

1. 注意业务逻辑的单一职责，不要出现重复的业务逻辑实现。不同微服务，不要开发相同业务。
   > 比如在订单模块中需要查询用户，应该跟用户模块进行通信获取，而不是直接在订单模块中访问数据库（即应该是 service 之间的互相调用，而不能够访问不该访问的 mapper）
2. 微服务数据独立，不要访问其他微服务的数据库
3. 微服务可以将自己的业务暴露为接口，供其他微服务调用（RESTful 接口）
4. 不同微服务都应该有自己独立的数据库（物理上防止其他微服务访问自己的数据库）

## 服务间调用

> 原理：通过 HTTP 请求请求 RESTful 接口获取数据，与具体语言无关，只要知道对方的 url 和需要的参数即可获取，因此只需要知道 Java 代码中如何发起 HTTP 请求来获得相应数据

RestTemplate：Spring 提供的 HTTP 请求方式

```java
/**
 * 将 RestTemplate 注册到 Spring 容器中
 */
@Bean
public RestTemplate restTemplate() {
    return new RestTemplate();
}

/**
 * 通过订单id获取订单和对应的用户信息
 */
public Order queryOrderById(Long orderId) {
    Order order = orderMapper.queryOrderById(orderId);
    // 利用 RestTemplate 发起 HTTP 请求查询用户
    // url 路径
    String url = "http://localhost:8081/user/" + order.getUserId();
    // 发起请求
    User user = restTemplate.getForObject(url, User.class);
    order.setUser(user);
    return order;
}
```

## Eureka 服务中心

### 服务提供者与服务消费者

1. 服务提供者：一次业务中，被其他微服务调用的服务。（提供接口给其他微服务）
2. 服务消费者：一次业务中，调用其他微服务的服务（调用其他微服务提供的接口）

> 服务A调用服务B，服务B调用服务C，服务B是什么角色？
> 
> 相对的，相对A是服务提供者，相对C是服务消费者。（一个服务既可以是提供者也可以是消费者）

### Eureka 注册中心

#### 服务调用出现的问题

1. ip和端口硬编码，地址变化和多实例都会导致硬编码失效和失去扩展性
2. 如果有多个服务提供者，消费者如何选择
3. 消费者如何得知服务提供者的健康状态

#### Eureka的作用

1. eureka-server：注册中心
2. eureka-client：各个微服务
   1. **每个微服务**在启动时将自己注册到 eureka-server 上。某个服务消费者在需要调用其他微服务时，通过其他微服务来获取可用的微服务。（不用硬编码ip和端口）
   2. 与 eureka-server 通过心跳检测机制来持续注册，每30s发送一次心跳检测，如果超过时间还未发送，则会取消该 client 在 eureka-server 上的注册（感知服务状态）
   3. 服务消费者通过 eureka-server 获取需要的微服务的所有实例（byName，服务名称是很重要的），通过负载均衡选择服务来进行远程调用（负载均衡来选择提供者）

> 需要注意的是，在 eureka 中服务消费者是通过服务名称来获取对应的服务提供者的，因此 `spring.application.name` 是十分重要的，需要在 yaml 中明确定义且约定好服务的名称。

#### Eureka示例

1. pom.xml 中导入对应的依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

2. 启动类上加上 `@EnableEurekaServer` 注解
3. application.yml 中编写 eureka-server 配置

```yaml
server:
    port: 10086

# 配置微服务名称
spring:
    application:
        name: eureka-server

# eureka 地址
eureka:
    client:
        # 禁止将自己注册到注册中心上，用于单机模式下
        fetch-registry: false
        register-with-eureka: false
        service-url:
            defaultZone: http://127.0.0.1:10086/eureka/
```

#### 服务注册

1. pom.xml 导入配置

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

2. 启动类上加上 `@EnableEurekaClient` 注解 
3. application.yml 文件中添加

```yaml
spring:
    application:
        name: user-service

eureka:
    client:
        fetch-registry: true
        register-with-eureka: true
        service-url:
            defaultZone: http://127.0.0.1:10086/eureka/
```

#### 服务拉取

1. 通过服务名称替代ip + 端口
2. 将 RestTemplate 添加 `@LoadBalanced` 来负载均衡

```java
/**
 * 将 RestTemplate 注册到 Spring 容器中
 * @LoadBalanced 负载均衡
 */
@Bean
@LoadBalanced
public RestTemplate restTemplate() {
    return new RestTemplate();
}

/**
 * 通过订单id获取订单和对应的用户信息
 */
public Order queryOrderById(Long orderId) {
    Order order = orderMapper.queryOrderById(orderId);
    // 利用 RestTemplate 发起 HTTP 请求查询用户
    // url 路径
    String url = "http://user-service/user/" + order.getUserId();
    // 发起请求
    User user = restTemplate.getForObject(url, User.class);
    order.setUser(user);
    return order;
}
```
