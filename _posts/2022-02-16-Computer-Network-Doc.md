---
layout:     post
title:      Computer Network Doc
subtitle:   南京大学大二下互联网计算笔记(个人向，更新中)
date:       2022-2-16
author:     ZY
header-img: img/bg4.jpg
catalog: true
tags:
    - Computer Network
---



# 互联网计算



## 1 计算机网络及其参考模型

### 1.1 Content

1. Overview of Computer Network
2. OSI Reference Model
3. TCP/IP Model
4. Network Topology
5. Network Devices



### 1.2 Networks

#### What is a network

A network is an intricately connected system of objects, devices, or people.

> 网络是由对象、设备或人员组成的错综复杂的连接系统

Companies created networks.



#### Data Networks Classifications

1. LAN (Local Area Networks)
2. WAN (Wide Area Networks)

> 可以将网络分为 node 和 link。



**Local Area Networks**

* Operate locally (cover small areas)
* Multi-user access
* High speeds expected (up to Gbps / 10Gbps)
* Error rate is easily controlled

**Wide Area Networks**

* Operate over larger areas
* Access over serial links, optical links, etc.
* Traditionally, have Lower speeds
* Error rate can not be easily controlled



#### Internet with Multi-layer ISP structure



### 1.3 计算机网络中的一些概念

#### 1.3.1 Data

* Data is sent in bits, 1s and 0s

* Data is not the information itself

* Data is an encoded form of information which is a series of electrical impulses/optical signals into which information is transmitted for sending

	> 数据不是信息本身
	>
	> 数据是信息的编码形式，它是一系列电脉冲/光信号，信息被传输到其中以进行发送



#### 1.3.2 Data Packets

* For transmission, computer data is often broken into small, easily transmitted units
	* Using the OSI model, these units can be called packets, or frames or segments
* Why data packets?
	* Computers can take turns sending packets
	* If packet is lost, only small amount of data must be retransmitted.
	* Data can take different paths.

> * 对于传输，计算机数据通常被分解成易于传输的小单元
> 	* 使用 OSI 模型，这些单元可以称为数据包、帧或段
> * 为什么是数据包？
> 	* 电脑可以轮流发包
> 	* 如果数据包丢失，只需重新传输少量数据。
> 	* 数据可以采用不同的路径。



#### 1.3.3 Protocol（协议）

* It's possible for different types of computer systems to communicate
* All devices must speak the same "language" or use the same protocol（use same set of rules）

> * 不同类型的计算机系统可以进行通信
> * 所有设备必须使用相同的“语言”或使用相同的协议（使用相同的规则集）



#### 1.3.4 Source and Destination

* Source (源) address specifies the identity of the computer sending the packet
* Destination (终) address specifies the identity of the computer designated to receive the packet



#### 1.3.5 Media Types



#### 1.3.6 Digital Bandwidth (带宽)

Bandwidth is the measure of how much information can flow from one place to another in a given amount of time.

> 带宽是衡量在给定时间内可以从一个地方流向另一个地方的信息量。



#### 1.3.7 Throughput （通量）

> Throughput $\le$ bandwidth



### 1.4 OSI Reference Model

> OSI: Open System Interconnection

* Proposed by International Organization for Standardization (ISO)
* A network model that help network builders implement networks that could communicate and work together
* Describes how information or data moves from one computer through a network to another computer
* a layered communication process
	* Each layer performs a specific task

#### 1.4.1 七层模型

1. Application (应用层) -- User interface
2. Presentation (表示层) -- Data presentation and encryption
3. Session (会话层) -- Inter-host connection
4. Transport (传输层) -- End-to-end connections
5. Network (网络层) -- Addresses and best path
6. Data Link (数据传输层) -- Access to media
7. Physical (物理层) -- Binary transmission

![](https://s2.loli.net/2022/02/16/KiEbWeLkVw5ncxT.png)

##### 1.4.1.1 物理层





#### 1.4.2 层级模型的好处

* 减少复杂性
* 标准化接口
* 促进模块化工程
* 确保可互操作的技术
* 加速进化
* 简化教学和学习

> 上三层一般被称为 **应用层**，因为它们处理用户界面、数据格式和应用程序访问。
>
> 下次层一般称为 **数据流层**，因为它们控制网络上消息的物理传递。