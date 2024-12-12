---
title: Nacos config server and OpenFeign
date: 2022-07-21 23:49:37
description: Nacos 配置中心和 OpenFeign
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku69.jpg
tags:
    - 微服务
    - SpringCloud
categories:
    - Cloud Native
    - 微服务
    - SpringCloud
---

## Nacos 配置管理

### 统一配置管理

**为什么需要配置管理**

1. 配置更新会导致服务重启
2. 多个服务的相同配置更新需要修改多个地方，可修改性不足。

<!-- more -->

**什么配置需要放在配置管理中**

1. 需要热更新、随着时间可能需要修改、开关的配置（比如某些业务逻辑的切换，可能在某个时间段是有切换需求的）
2. 统一的配置格式：比如日期格式，可能随着时间推移需要的日期格式会发生变化，此时只需要在配置管理中修改即可

> 像数据库url等一些基本不会变动的，不一定要放在配置中心中

**Nacos 中添加配置管理**

1. 命名：${servicename}-${profile}.yaml
2. 内容：遵循上面的应该放在配置管理中的配置的原则

**统一配置管理**

之前的配置获取步骤：

1. 项目启动
2. 读取本地配置文件 application.yml
3. 创建spring容器
4. 加载bean

> 何时读取 Nacos 中的配置文件呢？
> 
> 项目启动后先读取 Nacos 中的配置文件，项目启动后应该要怎么知道如何读取 Nacos 中的配置文件呢？bootstrap.yml
> 
> 在 bootstrap.yml 中需要配置好 Nacos 中的配置文件地址等信息，以保证在读取 application.yml 之前已经读取了 Nacos 中的配置文件

现在的配置获取步骤

1. 引入依赖

```xml
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
</dependency>
```

2. resource 目录下添加一个 bootstrap.yml 文件，该文件是引导文件，是由 ApplicationContext 加载的，优先级比 application.yml 高（优先加载）。

```yml
spring:
    application:
        name: user-service # 服务名称
    profiles:
        active: dev # 开发环境
    cloud:
        nacos:
            server-addr: localhost:8848
            config:
                file-extension: yaml # 文件后缀名
```

> 服务名称-开发环境.文件后缀名 = 在 Nacos 中配置的文件 dataId

### 配置热更新

1. 通过 `@RefreshScope` 来进行热更新（只对 `@Value` 注解标记的起作用）
2. 如果通过 `@ConfigurationProperties(prefix = "xxx")` 进行注解，则不需要其他的添加，会自动刷新。

### 配置共享

一个微服务在不同环境下可能会共享相同的配置，在微服务启动的时候项目会从 Nacos 读取多个配置文件

1. [spring.application.name]-[spring.profiles.active].yaml 例如：userservice-dev.yaml
2. [spring.application.name].yaml 例如：userservice.yaml -- 与环境没有关系的配置关系

> 配置文件中相同属性的优先级（服务名-profile.yml > 服务名.yml > 本地配置，即一个值如果有了后面加载的配置文件不会覆盖）

### 搭建 Nacos 集群

## HTTP 客户端 OpenFeign

### Feign 替代 RestTemplate

**RestTemplate 方式调用存在的问题**

```java
String url = "http://userservice/user/" + order.getUserId();
User user = restTemplate.getForObject(url, User.class);
```

这段代码存在以下的问题

1. 代码可读性差、编程体验不统一，在业务逻辑中参杂了远程调用。
2. 参数复杂URL难以维护（post请求的body参数，或者过多的param参数）

Feign：声明式的HTTP客户端，[Open Feign](https://github.com/OpenFeign/feign)。其作用就是帮助我们优雅的实现HTTP请求的发送，解决上面提到的问题。使用Feign的步骤如下：

1. 引入依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2. 在 service 启动类添加注解 `@EnableFeignClients` 开启 Feign 的功能
3. 编写 Feign 客户端

```java
@FeignClient("user-service", path = "/user")
public interface UserFeign {

    /**
     * 和 controller 一样的 RESTful 接口
     */
    @GetMapping("/{id}")
    User findById(@PathVariable("id") Long id);
}
```

4. 直接方法调用即可

### 自定义配置

Feign 运行自定义配置来覆盖默认配置，可以修改的配置如下（不仅仅只有这些）：

| 类型                | 作用             | 说明                                                     |
| ------------------- | ---------------- | -------------------------------------------------------- |
| feign.Logger.Level  | 修改日志级别     | 包含四种不同的级别：NONE、BASIC、HEADERS、FULL           |
| feign.codec.Decoder | 响应结果的解析器 | HTTP远程调用的结果做解析，例如解析json字符串为java对象   |
| feign.codec.Encoder | 请求参数编码     | 将请求参数编码，便于通过http请求发送                     |
| feign.Contract      | 支持的注解格式   | 默认是SpringMVC的注解                                    |
| fegin.Retryer       | 失败重试机制     | 请求失败的重试机制，默认是没有的，不过会使用Ribbon的重试 |

一般我们需要配置的就是日志级别，下面以配置日志级别作为例子来看如何修改

1. 修改配置文件
   1. 全局生效：
      ```yaml
      feign:
        client:
            config:
                default: # 这里的default就是全局配置，如果填写服务名称，则是针对某个微服务的配置
                    loggerLevel: FULL # 日志级别
      ``` 
   2. 局部生效：
      ```yaml
      feign:
        client:
            config:
                userservice:
                    loggerLevel: FULL # 日志级别
      ``` 
2. 使用 java 代码
   ```java
   @Configuration
   public class FeignClientConfiguration {
        @Bean
        public Logger.Level feignLogLevel() {
            return Logget.Level.BASIC;
        }
   }
   ```
   1. 如果是全局配置，则放到 `@EnableFeignClients(defaultConfiguration = FeignClientConfiguration.class)`
   2. 如果是局部配置，则放到 `@FeignClient(value = "userservice", configuration = FeignClientConfiguration.class)`

### Feign 使用优化

Feign 底层客户端实现

1. URLConnection：默认实现，不支持连接池
2. Apache HttpClient：支持连接池
3. OKHttp：支持连接池

优化Feign性能主要包括：

1. 使用连接池代替默认的 URLConnection
2. 日志级别，最好用 BASIC 或者 NONE

**Feign添加HTTPClient的支持**

1. 引入依赖
   ```xml
   <dependency>
        <groupId>io.github.openfeign</groupId>
        <artifactId>feign-httpclient</artifactId>
   </dependency>
   ```
2. 配置文件中配置连接池
   ```yaml
   feign:
        client:
            config:
                default:
                    loggerLevel: BASIC
        httpclient:
            enabled: true # 开启feign对HttpClient的支持
            max-connections: 200 # 最大连接数
            max0connections-per-route: 50 # 每个路径的最大连接数
   ```

### 最佳实践

#### 继承

> 给消费者的 FeignClient 和提供者的 controller 定义统一的父接口作为标准

```java
public interface UserAPI {

    @GetMapping("/{id}")
    User findById(@PathVariable Long id);
}
```

```java
@FeignClient(value = "userservice", path = "/user")
public inteface UserClient extends UserAPI {

}
```

```java
@RestController
@RequestMapping("/user")
public class UserController implements UserAPI {


}
```

> 这么做有两个确定：一个是因为 SpringMVC 所导致的参数注解不会被继承；一个是因为 feign 默认是不支持多继承 or 多层继承的。且这样会导致两个接口的耦合程度十分高

#### 抽取

> 将 FeignClient 抽取为独立的模块，并且把接口有关的 POJO、默认的 Feign 配置都放到这个模块中，提供给所有消费者使用

将 Feign 远程调用有关的都集中在一个模块中，打包成 jar 包引入依赖即可，但这样会导致不必要的依赖引入（可能仅仅需要其中的一些）
