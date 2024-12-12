---
title: Ribbon and Nacos
date: 2022-07-20 16:33:42
description: Ribbon 负载均衡和 Nacos 注册中心
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku68.jpg
tags:
    - 微服务
    - SpringCloud
categories:
    - Cloud Native
    - 微服务
    - SpringCloud
---

## Ribbon 负载均衡

### 负载均衡原理

Ribbon 源码。

1. `LoadBalancerInterceptor` 负载均衡拦截器拦截 HTTP 请求
2. `RibbonLoadBalancerClient` 获取url中的服务id（hostname，即前面所说的user-service），交给 `DynamicServerListLoadBalancer` 来获取该服务id对应的服务列表
3. `DynamicServerListLoadBalancer` 到 Eureka 获取对应的服务列表，通过 `IRule` 接口对应的规则来获得负载均衡选择到的服务
4. 返回给 `RibbonLoadBalancerClient` 进行请求，返回结果

<!-- more -->
  
### 负载均衡策略

Ribbon 的负载均衡规则是一个叫做 IRule 的接口来定义的，每一个子接口都是一种规则

| 内置负责均衡规则类        | 规则描述                                                                                                                                                                                                                                                                                                                                                                                                            |
| ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RoundRobinRule            | 简单轮询服务列表来选择服务器。它是Ribbon默认的负载均衡规则                                                                                                                                                                                                                                                                                                                                                          |
| AvailabilityFilteringRule | 对以下两种服务器进行忽略 </br> 1. 在默认情况下，这台服务器如果3次连接失败，这台服务器就会被设置为“短路”状态。短路状态将持续30秒，如果再次连接失败，短路的持续时间就会几何级地增加 </br> 2. 并发数过高的服务器。如果一个服务器的并发连接数过高，配置了 AvailabilityFilteringRule 规则的客户端也会将其忽略。并发连接数的上线，可以由客户端的 [clinetName].[clientConfigNameSpace].ActiveConnectionsLimit 属性进行配置 |
| WeightedResponseTimeRule  | 为每一个服务器赋予一个权重值。服务器响应时间越长，这个服务器的权重就越小。这个规则会随机选择服务器，这个权重值会影响服务器的选择                                                                                                                                                                                                                                                                                    |
| ZoneAvoidanceRule         | 以区域可用的服务器为基础进行服务器的选择。使用Zone对服务器进行分类，这个Zone可以理解为一个机房、一个机架等。而后再对Zone内的多个服务做轮询                                                                                                                                                                                                                                                                          |
| BestAvailableRule         | 忽略那些短路的服务，并选择并发数较低的服务器                                                                                                                                                                                                                                                                                                                                                                        |
| RandomRule                | 随机选择一个可用的服务器                                                                                                                                                                                                                                                                                                                                                                                            |
| RetryRule                 | 重试机制的选择逻辑                                                                                                                                                                                                                                                                                                                                                                                                  |

**如何调整负载均衡规则**

1. 代码方式

```java
@Bean
public IRule randomRule() {
    // 将负载均衡方式变成随机
    return new RandomRule();
}
```

2. 配置文件方式：yml中配置修改规则

```yaml
user-service:
    ribbon:
        NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RandomRule
```

### 懒加载

Ribbon 默认采用的是**懒加载**，即第一次访问的时候才会去创建 `LoadBalanceClient`，请求时间会很长。采用**饥饿加载**会在项目启动时创建，降低第一次访问的耗时，通过下面的配置可以开启饥饿加载。

```yaml
ribbon:
    eager-load:
        enabled: true
        clients: user-service # 指定对 user-service 的服务开启饥饿加载
```

## Nacos 注册中心

[Nacos](https://nacos.io)

### 服务注册

> 和 Eureka 类似

1. 引入依赖
2. 配置nacos地址，默认端口为8848

```yaml
spring:
    cloud:
        nacos:
            server-addr: localhost:8848 # nacos 服务端地址
```

### 服务分级存储模型

服务 - 集群 - 实例：为了应对容灾和多实例的负载均衡问题，将服务分级，每个地域作为一个集群，集群中包含多个服务的实例。优先访问本地的集群，本地集群不可用的时候才访问其他集群。

**Nacos中配置集群属性**

在yml中配置

```yaml
spring:
    cloud:
        nacos:
            discovery:
                cluster-name: HangZhou # 集群的名字
```

### NacosRule 负载均衡

如何做到优先访问本地集群的设置？与上面Ribbon相同，需要配置负载均衡的策略：

```yaml
userservice:
    ribbon:
        NFLoadBalancerRuleClassName: com.alibaba.cloud.nacos.ribbon.NacosRule # 负载均衡规则
```

NacosRule 负载均衡策略

1. 优先选择同集群服务实例列表
2. 本地集群找不到提供者，采取其他集群寻找，并且会有警告
3. 确定了可用实例列表后，再采用随机负载均衡挑选实例 

### 服务实例的权重设置

实际部署可能会出现以下的情况：服务器设备性能有差异，部分实例所在机器性能较好，另一些较差，我们希望性能好的机器承担更多的用户请求

> Nacos 提供了权重配置累控制访问频率，权重越大则访问频率也越高

Nacos 控制台可以设置实例权重值，范围为0-1，可以将性能较差的实例权重调小。权重为0的不会被访问，可以实现平滑替换。

### Nacos 环境隔离

Nacos 中服务存储和数据存储的最外层都是一个名为namespace的东西，用来做最外层的隔离，用来对不同环境做隔离。namespace之下还有group的划分，可以将关联度较高的服务作为一个group。

在 Nacos 控制台创建新的命名空间，生成的id在服务中配置对应的namespace

```yaml
spring:
    cloud:
        nacos:
            namespace: ${namespace-id} # 对应的namespace id
```

### Eureka 和 Nacos 的对比

Nacos 会将实例划分为临时实例和非临时实例，默认情况下所有实例都是临时实例。

1. 临时实例采用的是心跳检测（服务向注册中心发消息），和 Eureka 相同（Nacos 频率较高），如果检测不到则直接剔除
2. 非临时实例采用的是主动询问（注册中心向服务发消息），如果挂了也不会剔除，而是标记为不健康，保留。

**如何设置为非临时实例**

```yaml
spring:
    cloud:
        nacos:
            ephemeral: false # 是否为临时实例
```

在 Eureka 中则没有临时实例和非临时实例之分，所有的实例都是类似于 Nacos 中的非临时实例。同时，对于服务列表的变更，Eureka 和 Nacos 也有差别

1. Eureka 采用的是定时拉取服务缓存（pull，每隔30s一次），但是这会导致如果30s内有服务实例变化，其他服务无法知道。
2. Nacos 采用的是定时拉取服务和主动推送变更消息（pull and push）结合，如果有服务挂了，那么会及时通知服务的消费者。

在集群中，两者的模式也有差别

1. Nacos集群默认采用AP模式，当集群中存在非临时实例时，采用CP模式；
2. Eureka采用AP模式