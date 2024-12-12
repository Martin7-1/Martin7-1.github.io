---
title: K8s Introduction
date: 2022-07-15 23:55:20
description: K8s简介
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku65.jpeg
tags:
    - K8s
    - Cloud Native
    - 微服务
categories:
    - Cloud Native
    - K8s
---

## 应用部署方式演变

1. 传统部署：直接部署在物理机上，简单，不需要要其他技术参与，但是不能够为应用程序资源定义边界
2. 虚拟机部署：程序环境之间不会相互产生影响，提供一定程度的安全性；但是增加了操作系统，浪费了部分资源
3. 容器化部署：保证每个容器拥有自己的文件系统、CPU、内存、进程空间等。运行应用程序所需要的资源都被容器包装，并和底层基础架构解藕。可以跨云服务商，跨Linux操作系统发行版进行部署

<!-- more -->

![部署方式演变](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220715162551.png)

**容器的优势**

1. 敏捷性：敏捷应用程序的创建和部署；和使用VM镜像相比，提高了容器镜像创建的简便性和效率。
2. 及时性：持续开发、集成和部署；通过快速简单的回滚（由于镜像的不可变性），支持可靠且频繁的容器镜像的构建和部署
3. 解耦性：关注开发和运维的分离；在构建、发布时创建应用程序的容器镜像，而不是在部署的时候，从而将应用程序和基础架构分离
4. 可观测性：不仅可以显示操作系统级别的信息和指标，还可以显示应用程序的运行状态和其他指标信号。
5. 跨平台：跨开发、测试和生产的环境一致性；在便捷式的计算机上和在云上相同的运行
6. 可移植：跨云和Linux发行版本的可移植性；可以在 Ubuntu、CentOS、RedHat、本地、Kubernetes 和其他任何地方运行
7. 简易性：以应用程序为中心的管理；提高抽象级别，从在虚拟硬件上运行 OS 到使用逻辑资源在 OS 上运行应用程序。
8. 大分布式：松散耦合、分布式、弹性、解放的微服务；应用程序被分解成较小的独立部分，并且可以动态的部署和管理 -- 而不是在一台大型单机上整体运行（垂直扩展是有上限的）
9. 隔离性：资源隔离；可预测应用程序性能
10. 高效性：资源利用；高效率和高密度

> 容器化部署的问题：
> 1. 故障停机时，如何让另外一个容器立刻启动去替补停机的容器
> 2. 并发访问量变大的时候，怎么样做到横向扩展容器数量

**容器编排问题**：容器管理的问题称为容器编排问题，为了解决这些问题，就产生了一些容器编排的软件：

1. Swarm：Docker 自己的容器编排工具
2. Mesos：Apache 的一个资源统一管控工具
3. Kubernetes：Google 开源的容器编排工具

## Kubernetes 简介

kubernetes 的本质是一组服务器集群，它可以在集群的每个节点上运行特定的程序，来对节点中的容器进行管理。它的目的就是实现资源管理的自动化，主要提供了以下的主要功能：

* 自我修复：一旦某一个容器崩溃，能够在1秒左右迅速启动新的容器
* 弹性伸缩：可以根据需要，自动对集群中正在运行的容器数量进行调整
* 服务发现：服务可以通过自动发现的形式找到它所依赖的服务
* 负载均衡：如果一个服务启动了多个容器，能够自动实现请求的负载均衡
* 版本回退：如果发现新发布的程序版本有问题，可以立即回退到原来的版本
* 存储编排：可以根据容器自身的需求自动创建存储卷

## Kubernetes 组件

一个 kubernetes 集群主要是由控制节点（master）、工作节点（node）组成，每个节点上都会安装不同的组件。

![K8s组件](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220715163221.png)

1. master：集群的控制平面，负责集群的决策（管理）。
   1. ApiServer：资源操作的唯一入口，接受用户输入的命令，提供认证、授权、API注册和发现等机制
   2. Scheduler：负责集群资源调度，按照预定的调度策略将Pod调度到相应的Node节点上
   3. ControllerManager：负责维护集群的状态，比如程序部署安排、故障检测、自动扩展、滚动更新等
   4. Etcd：负责存储集群中各种资源对象的信息（K8s环境启动后，master和node都会将自身信息存储到Etcd中）
2. node：集群的数据平面，负责为容器提供运行环境（真正干活的）
   1. Kubelet：负责维护容器的生命周期，即通过控制docker，来创建、更新、销毁容器
   2. KubeProxy：负责提供集群内部的服务发现和负载均衡
   3. Dcoker：负责节点上容器的各种操作
3. Pod： 
   1. docker run 启动的是一个 container（容器），容器是 Docker 的基本单位，一个应用就是一个容器。
   2. kubectl run 启动的是一个应用称为一个 Pod ，Pod 是 Kubernetes 的基本单位。 
      1. Pod 是对容器的再一次封装。
      2. Pod 类似于 Java 日志体系中的 Slf4j ，而 Docker 中的容器类似于 Java 日志体系中的 Logback 等日志实现。
      3. 一个容器往往代表不了一个基本应用，如：博客系统（WordPress，PHP + MySQL）；但是一个 Pod 可以包含多个 Container，一个 Pod 可以代表一个基本的应用。

## Kubernetes 概念

1. Master：集群控制节点，每个集群需要至少一个master节点负责集群的管理
2. Node：工作负载节点，由 master 分配容器到这些 node 工作节点上，然后 node 节点上的 docker 负责容器的运行
3. Pod：kubernetes 的最小控制单元，容器都是运行在 pod 中的，一个 pod 中可以有1个或者多个容器
4. Controller：控制器，通过它来实现对 pod 的管理，比如启动 pod、停止 pod、伸缩 pod 的数量等（一类概念，有多种控制器）
5. Service：pod 对外服务的统一入口，下面可以维护着同一类的多个 pod
6. Label：标签，用于对 pod 进行分类，同一类 pod 会拥有相同的标签
7. NameSpace：命名空间，用来隔离 pod 的运行环境

## Kubernetes 部署 Tomcat 


1. 开机默认所有节点的 kubelet 、master 节点的s cheduler（调度器）、controller-manager（控制管理器）一直监听 master 的 api-server 发来的事件变化。 
2. 程序员使用命令行工具： kubectl ； kubectl create deploy tomcat --image=tomcat8（告诉 master 让集群使用 tomcat8 镜像，部署一个 tomcat 应用）。 
3. kubectl 命令行内容发给 api-server，api-server 保存此次创建信息到 etcd 。 
4. etcd 给 api-server 上报事件，说刚才有人给我里面保存一个信息。（部署Tomcat[deploy]） 
5. controller-manager 监听到 api-server 的事件，是 （部署Tomcat[deploy]）。 
6. controller-manager 处理这个 （部署Tomcat[deploy]）的事件。controller-manager 会生成 Pod 的部署信息【pod信息】。 
7. controller-manager 把 Pod 的信息交给 api-server ，再保存到 etcd 。 
8. etcd 上报事件【pod信息】给 api-server 。 
9. scheduler 专门监听 【pod信息】 ，拿到 【pod信息】的内容，计算，看哪个节点合适部署这个 Pod【pod 调度过后的信息（node: node-02）】。 
10. scheduler 把 【pod 调度过后的信息（node: node-02）】交给 api-server 保存给 etcd 。 
11. etcd 上报事件【pod调度过后的信息（node: node-02）】，给 api-server 。 
12. 其他节点的 kubelet 专门监听 【pod 调度过后的信息（node: node-02）】 事件，集群所有节点 kubelet 从 api-server 就拿到了 【pod调度过后的信息（node: node-02）】 事件。 
13. 每个节点的 kubelet 判断是否属于自己的事情；node-02 的 kubelet 发现是他的事情。 
14. node-02 的 kubelet 启动这个 pod。汇报给 master 当前启动好的所有信息。 


## Reference

1. [Kubernetes（v1.21）简介](https://www.yuque.com/fairy-era/yg511q/sy5g50)
2. [Kubernetes](https://kubernetes.io/zh-cn/)


