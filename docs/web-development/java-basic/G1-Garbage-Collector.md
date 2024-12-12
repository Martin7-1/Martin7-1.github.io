---
title: G1 Garbage Collector
date: 2023-07-18 19:16:30
description: G1 Garbage Collector
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku94.jpeg
tags:
   - Java
   - JVM
categories:
   - Java
   - Advanced
---

## HotSpot Architecture

HotSpot JVM 架构主要有以下几个部分组成：

1. Class Loader Subsystem
2. Runtime Data Areas
   1. Method Area
   2. Heap
   3. Java Threads
   4. Program Counter Registers
   5. Native Internal Threads
3. Execution Engine
   1. JIT Compiler
   2. Garbage Collector
4. Native Method Inteface

<!-- more -->

![HotSpot JVM: Architecture](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20230718202028.png)

在 HotSpot JVM 中，和性能相关的组件有 Heap（堆，对象存放的地方，在 JVM 启动时由被选择的 GC 来管理）、JIT Compiler、Garbage Collecor（垃圾回收器）。

### Performance Basics

#### Responsiveness

响应能力反映了应用/系统对一个请求的响应的快速，比如：

1. 桌面 UI 对事件的响应速度（比如点击按钮等）
2. 网页对于请求的响应速度（比如返回一个页面 / 数据等）
3. 数据库执行结果返回的时间快慢

对于专注于提高响应能力的应用来说，较长的停顿时间（pause time）是不能接受的。

#### Throughput

吞吐量反映了应用/系统在一段时间里面的产出最大化，比如：

1. 一段时间内事务完成的数量
2. 一段时间内一批次任务的完成数量
3. 一段时间内数据库完成的查询次数

较高的停顿时间在专注于吞吐量的程序中是可以接受的。由于高吞吐量应用程序关注较长时间内的基准测试，因此不需要考虑快速响应时间

## G1 Garbage Collector

G1(Garbage First) 垃圾回收器是一款面向服务器，目前在于多核、大内存及其的垃圾回收器。它的目标是在实现高吞吐量的同事，解决 GC 暂停时间较长的问题，在 Oracle JDK7u4 及其之后的版本被完全支持，在 JDK9 开始被作为默认的垃圾回收器使用，G1 垃圾回收器适用于如下的应用程序：

1. 可以和应用程序线程并发的进行操作，就像 CMS 垃圾回收器一样
2. 压缩自由空间，没有因为 GC 引起的较长的暂停时间
3. 需要更多可预测的 GC 暂停时间
4. 不希望牺牲太多吞吐量上的性能

G1 替换了 CMS 成为默认的垃圾回收器主要是因为两者之间的这些不同：

1. G1 是一个压缩垃圾回收器（compacting collector）。G1足够紧凑，完全避免使用细粒度空闲列表进行分配，而是依赖于区域（regions）。这样大大简化了垃圾回收器的部分，而且最大程度上消除了可能的碎片。
2. G1 相比 CMS 提供了可预测性极好的垃圾回收机制，允许用户来指定想要的暂停目标（即灵活性大大提升，可以方便用户根据业务需求来定制不同的配置等）。

### G1 Operational Overview

早期的垃圾回收器（如 serial、parallel、CMS）都是将堆划分为固定内存大小的三个区域：新生代（Young Generation，包括 eden 和 suvivor space）、老年代（Old Generation）和永久代（Permanent Generation）。

![Hotspot Heap Structure](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20230718212105.png)

G1 垃圾回收器则提供了完全不同的方式来管理堆

![20230718212153](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20230718212153.png)

堆被划分为一系列相同大小的区域（region），每个在虚存中都是连续的空间。和早期的垃圾回收器一样，一个区域的角色是相同的（eden，suvivor，old，即一个区域内不会即存放 eden 的对象又存放 old 的对象）。但是对于每个代而言不会有固定的大小（每个代都是由多个区域组成的，这样就可以很方便、灵活的去扩展代的大小）。

当执行垃圾回收的时候，G1 执行类似于 CMS 的操作。G1 执行**并发的标记阶段**来决定哪些对象还需要存活在堆中。在标记阶段结束后，G1 知道哪一些区域是比较空的，为了能够获得较大的空闲区域，G1 会先收集这些区域（这也是为什么这个垃圾回收器会叫做 Garbage First）。G1 会在那些有较多可回收对象的区域来展开垃圾回收和压缩工作。G1 使用一个暂停预测模型来实现用户决定（user-defined）的暂停时间目标。

G1 将对象从堆的一个或多个区域复制到堆上的单个区域，并在此过程中压缩和释放内存。垃圾回收在多处理器上并行执行，以减少暂停时间并提高吞吐量。因此，对于每个垃圾回收，G1 都在用户定义的暂停时间内持续工作以减少碎片。这超出了之前垃圾回收器的能力：CMS 垃圾回收器不执行压缩。ParallelOld 垃圾回收仅执行整个堆压缩，这会导致相当长的暂停时间。

需要注意的是 G1 **并不是一个实时（real-time）的垃圾回收器**。

> NOTE: G1 具有并发（与应用程序线程一起运行，例如，细化（refinement）、标记（marking）、清理（cleanup））和并行（多线程，比如 Stop the World）阶段。完全垃圾回收仍然是单线程的，但如果调整正确，应用程序应避免使用完整的 GC。

### G1 Footprint

如果从 ParallelOldGC 或者 CMS 垃圾回收器迁移到 G1，你可能会看到一个占用更大内存的 JVM 进程，这是因为大量和「accouting」相关的数据结构被存储，比如 Remembered Sets 和 Collection Sets

Remembered Sets：该数据结构用于跟踪对给定区域的对象引用。堆中的每个区域都有一个 RSet。RSet 支持对区域进行并行和独立的收集。RSET 的总体占用空间影响小于 5%。

Collection Sets：该数据结构存储将在 GC 中收集的区域集。在 GC 期间，CSet 中的所有实时数据都会被清空（复制/移动）。区域集可以是伊甸园（eden）、幸存者（suvivor）和/或老一代（old generation）。CSet 对 JVM 大小的影响小于 1%。

### Recommended Use Cases for G1

## Command Line Options and Best Practices

## Complete List of G1 GC Switches

| Option and Default Value             | Description                                                                                                                                                                |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| -XX:+UseG1GC                         | 使用 G1 垃圾回收器                                                                                                                                                         |
| -XX:MaxGCPauseMillis=n               | 设置最大的 GC 停顿时间，这是一个软目标，JVM 只会尽力去实现它，但是不会严格遵守这个设置的时间                                                                               |
| -XX:InitiatingHeapOccupancyPercent=n | 启动并发 GC 周期的（整个）堆占用百分比。它由 GC 使用，这些 GC 基于整个堆的占用率触发并发 GC 循环，而不仅仅是一代（例如，G1）。值 0 表示“执行恒定的 GC 循环”。默认值为 45。 |
| -XX:NewRatio=n                       | 新旧代的比例。默认值为 2                                                                                                                                                   |
| -XX:SurvivorRatio                    | eden/survivor 空间大小的比例，默认值为 8                                                                                                                                   |
| -XX:MaxTenuringThreshold=n           | 年轻代晋升老年代的最大年龄阈值，默认值为15                                                                                                                                 |
| -XX:ParallelGCThreads=n              | 在并发阶段的线程数。默认值根据 JVM 运行环境的不同而不同                                                                                                                    |
| -XX:ConcGCThreads=n                  | 在并发阶段的线程数。默认值根据 JVM 运行环境的不同而不同                                                                                                                    |
| -XX:G1ReservePercent=n               | 设置堆保留的百分比，作为一个虚假的最大值来减少晋升失败的可能性。默认值为 10                                                                                                |
| -XX:G1HeapRegionSize=n               | G1 区域的大小，默认值根据堆大小来决定。最小值为 1Mb，最大值为 32Mb                                                                                                         |

## Reference

1. [Getting Started with the G1 Garbage Collector](https://www.oracle.com/technetwork/tutorials/tutorials-1876574.html)
2. [Garbage First Garbage Collector Tuning](https://www.oracle.com/technical-resources/articles/java/g1gc.html?spm=a2c6h.12873639.article-detail.166.5c7371c8HKS6eD)
