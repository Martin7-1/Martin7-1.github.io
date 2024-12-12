---
title: Java SPI
date: 2022-11-17 14:12:31
description: Java SPI 了解
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku89.jpeg
tags:
    - Java
categories:
    - Java
    - Basic
---

## 前言

在面向对象的设计原则中，一般推荐模块之间基于接口编程，通常情况下调用方模块是不会感知到被调用方模块的内部具体实现。一旦代码里面涉及到具体实现类，就违反了开闭原则。如果需要替换一种实现，就需要修改代码。

<!-- more -->

为了实现在模块装配的时候不用在程序里面动态指明，这就需要一种服务发现机制。Java SPI 就是提供了这样一种机制：**为某个接口寻找服务发现的实例。这有点类似 IoC（Inversion of Control，控制反转）的思想，将装配的控制权移交到了程序之外。**

> 当不需要在程序中动态指明实现类的时候，我们就做到了开闭原则，即对扩展开放，对修改关闭，指定新的实现类不需要去修改原有的代码，因为原有的代码和现有的新的实现类都是遵循着相同的接口“规约”来进行开发的，因此不需要担心因为实现类的不同而造成的代码错误。

## 什么是 SPI

SPI，即 Service Provider Interface，是一种专门给服务提供者或者扩展框架功能的开发者去使用的接口。SPI 将服务接口和具体的服务实现分离开来，将服务调用方和服务实现者解耦，能够提升程序的扩展性、可维护性。修改或者替换服务实现并不需要去修改调用方。

很多框架都使用了 Java 的 SPI 机制，比如：

* Spring 框架
* 数据库加载驱动
* 日志接口
* Dubbo 的扩展实现
* ...

## SPI 和 API 有什么区别

![API and SPI](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20221117142744.png)

> 下面解释来自 JavaGuide
>
> 一般模块之间都是通过通过接口进行通讯，那我们在服务调用方和服务实现方（也称服务提供者）之间引入一个“接口”。当实现方提供了接口和实现，我们可以通过调用实现方的接口从而拥有实现方给我们提供的能力，这就是 API ，这种接口和实现都是放在实现方的。
> 当接口存在于调用方这边时，就是 SPI ，由接口调用方确定接口规则，然后由不同的厂商去根据这个规则对这个接口进行实现，从而提供服务。
> 举个通俗易懂的例子：公司 H 是一家科技公司，新设计了一款芯片，然后现在需要量产了，而市面上有好几家芯片制造业公司，这个时候，只要 H 公司指定好了这芯片生产的标准（定义好了接口标准），那么这些合作的芯片公司（服务提供者）就按照标准交付自家特色的芯片（提供不同方案的实现，但是给出来的结果是一样的）。

## 例子

### Service Provider Interface

> 下面通过实现一个简单的日志框架来演示 SPI 的实现原理，SLF4j 就是一种 Java 的日志接口，具体实现有 Logback、Log4j、Log4j2 等等，而且可以切换，切换的时候我们并不需要修改项目的代码，这就是 SPI 的一种体现。

新建 `Logger` 接口，这个就是 SPI，服务提供者接口，后面的所有服务提供者都基于这个接口来进行实现

```java
package com.zyinnju;

/**
 * @author zhengyi
 * @date 2022/11/17
 * @description 日志接口
 * @see <a href="https://javaguide.cn/java/basis/spi.html#service-provider-interface">Java SPI 详解</a>
 */
public interface Logger {

    /**
     * 打印 info 级别的日志
     *
     * @param msg 日志信息
     */
    void info(String msg);

    /**
     * 打印 debug 级别的日志
     *
     * @param msg 日志信息
     */
    void debug(String msg);
}
```

接下来是 `LoggerService` 类，这个类是为了给服务使用者（调用方）提供特定功能的，这个类是实现 Java SPI 机制的关键所在。其中使用了 `SerivceLoader` 来加载所有的接口实现类

```java
package com.zyinnju;

import java.util.ArrayList;
import java.util.List;
import java.util.ServiceLoader;

/**
 * @author zhengyi
 * @date 2022/11/17
 * @description 日志服务
 * @see <a href="https://javaguide.cn/java/basis/spi.html#service-provider-interface">Java SPI 详解</a>
 */
public class LoggerService {

    private static final LoggerService SERVICE = new LoggerService();
    /**
     * 真正的 logger
     */
    private final Logger logger;
    /**
     * 备选的所有 logger 实现
     */
    private final List<Logger> loggerList;

    private LoggerService() {
        // 加载所有的 logger 实现
        ServiceLoader<Logger> loader = ServiceLoader.load(Logger.class);
        List<Logger> list = new ArrayList<>();
        for (Logger log : loader) {
            list.add(log);
        }
        // LoggerList 是所有 ServiceProvider
        loggerList = list;
        if (!list.isEmpty()) {
            // Logger 只取一个，这里我们默认取加载的第一个
            logger = list.get(0);
        } else {
            logger = null;
        }
    }

    public static LoggerService getService() {
        return SERVICE;
    }

    public void info(String msg) {
        if (logger == null) {
            System.out.println("info 中没有发现 Logger 服务提供者");
        } else {
            logger.info(msg);
        }
    }

    public void debug(String msg) {
        if (loggerList.isEmpty()) {
            System.out.println("debug 中没有发现 Logger 服务提供者");
        }
        loggerList.forEach(log -> log.debug(msg));
    }
}
```

新建 `Main` 类（服务使用者，调用方），启动程序查看结果。

```java
package com.zyinnju;

/**
 * @author zhengyi
 * @date 2022/11/17
 * @description 程序主类
 * @see <a href="https://javaguide.cn/java/basis/spi.html#service-provider-interface">Java SPI 详解</a>
 */
public class Main {

    public static void main(String[] args) {
        LoggerService service = LoggerService.getService();

        service.info("Hello SPI");
        service.debug("Hello SPI");
    }
}
```

结果如下：

```bash
info 中没有发现 Logger 服务提供者
debug 中没有发现 Logger 服务提供者
```

这是因为我们此时只是空有接口，并没有为 `Logger` 接口提供任何的实现，所以输出结果中没有按照预期打印出相应的结果。接下来我们将其打包为 Jar 包。

### Service Provider

接下来新建一个项目来实现 `Logger` 接口。首先新建 `Logback` 类来实现 `Logger` 接口。

```java
package com.zyinnju.service.impl;

import com.zyinnju.service.provider.Logger;

/**
 * @author zhengyi
 * @date 2022/11/17
 * @description Logback 日志实现类
 * @see <a href="https://javaguide.cn/java/basis/spi.html#service-provider-interface">Java SPI 详解</a>
 */
public class Logback implements Logger {

    @Override
    public void info(String s) {
        System.out.println("Logback info 打印日志：" + s);
    }

    @Override
    public void debug(String s) {
        System.out.println("Logback debug 打印日志：" + s);
    }
}
```

实现 Logger 接口，在 src 目录下新建 META-INF/services 文件夹，然后新建文件 edu.jiangxuan.up.spi.Logger （SPI 的全类名），文件里面的内容是：edu.jiangxuan.up.spi.service.Logback （Logback 的全类名，即 SPI 的实现类的包名 + 类名）。

**这是 JDK SPI 机制 ServiceLoader 约定好的标准。**

这里先大概解释一下：Java 中的 SPI 机制就是在每次类加载的时候会先去找到 class 相对目录下的 META-INF 文件夹下的 services 文件夹下的文件，将这个文件夹下面的所有文件先加载到内存中，然后根据这些文件的文件名和里面的文件内容找到相应接口的具体实现类，找到实现类后就可以通过反射去生成对应的对象，保存在一个 list 列表里面，所以可以通过迭代或者遍历的方式拿到对应的实例对象，生成不同的实现。

所以会提出一些规范要求：文件名一定要是接口的全类名，然后里面的内容一定要是实现类的全类名，实现类可以有多个，直接换行就好了，多个实现类的时候，会一个一个的迭代加载。

接下来同样将 service-provider 项目打包成 jar 包，这个 jar 包就是服务提供方的实现。通常我们导入 maven 的 pom 依赖就有点类似这种，只不过我们现在没有将这个 jar 包发布到 maven 公共仓库中，所以在需要使用的地方只能手动的添加到项目中。

### Test

接下来进行测试，新建第三个项目 `spi-test`，然后导入上面的两个 Jar 包。

```java
package com.zyinnju;

import com.zyinnju.service.provider.LoggerService;

public class SPITest {

    public static void main(String[] args) {
        LoggerService loggerService = LoggerService.getService();
        loggerService.info("你好");
        loggerService.debug("测试Java SPI 机制");
    }
}
```

输出如下：

```bash
Logback info 打印日志：你好
Logback debug 打印日志：测试Java SPI 机制
```

通过使用 SPI 机制，可以看出服务（LoggerService）和 服务提供者两者之间的耦合度非常低，如果说我们想要换一种实现，那么其实只需要修改 service-provider 项目中针对 Logger 接口的具体实现就可以了，只需要换一个 jar 包即可，也可以有在一个项目里面有多个实现，这不就是 SLF4J 原理吗？

如果某一天需求变更了，此时需要将日志输出到消息队列，或者做一些别的操作，这个时候完全不需要更改 Logback 的实现，只需要新增一个服务实现（service-provider）可以通过在本项目里面新增实现也可以从外部引入新的服务实现 jar 包。我们可以在服务(LoggerService)中选择一个具体的 服务实现(service-provider) 来完成我们需要的操作。

## ServiceLoader

// TODO: 源码剖析

主要的流程就是：

1. 通过 URL 工具类从 jar 包的 /META-INF/services 目录下面找到对应的文件
2. 读取这个文件的名称找到对应的 spi 接口
3. 通过 InputStream 流将文件里面的具体实现类的全类名读取出来
4. 根据获取到的全类名，先判断跟 spi 接口是否为同一类型
5. 如果是的，那么就通过反射的机制构造对应的实例对象，将构造出来的实例对象添加到 Providers 的列表中。

## 总结

其实不难发现，SPI 机制的具体实现本质上还是通过反射完成的。即：我们按照规定将要暴露对外使用的具体实现类在 META-INF/services/ 文件下声明。

通过 SPI 机制能够大大地提高接口设计的灵活性，但是 SPI 机制也存在一些缺点，比如：

1. 遍历加载所有的实现类，这样效率还是相对较低的；
2. 当多个 ServiceLoader 同时 load 时，会有并发问题。

> 代码已上传至 [Github](https://github.com/Martin7-1/SPI-Example)

## Reference

1. [Java SPI 机制详解](https://javaguide.cn/java/basis/spi.html)
2. [Java Service Provider Interface](https://www.baeldung.com/java-spi)
3. [Introduction to the Service Provider Interfaces](https://docs.oracle.com/javase/tutorial/sound/SPI-intro.html)
