---
title: Computer Network Review (1)
date: 2022-02-17 16:08:03
description: 南京大学2022春季学期《互联网计算》课程笔记(1)
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku16.jpg
tags:
   - 互联网计算
   - Software Engineering
   - Computer Science
categories:
   - 软件工程
   - 互联网计算
---

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

多个设备（也称为主机）的互联，它们使用多条路径连接，以发送/接受数据。计算机网络还可以包括有助于两个不同设备之间通信的多个设备/介质，这些被称为网络设备，包括路由器（routers）、集线器（hub）和网桥（bridges）之类的东西

![](https://s2.loli.net/2022/02/16/SdRUYrkjwpmLZIl.png)



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
6. Data Link (数据链路层) -- Access to media
7. Physical (物理层) -- Binary transmission

![](https://s2.loli.net/2022/02/16/KiEbWeLkVw5ncxT.png)

##### 1.4.1.1 物理层（Physical Layer）

物理层是OSI模型中最底层的模型，它主要负责设备间真实的物理连接。物理层以`bits`的方式保存和传输数据。它负责将`bits`从一个节点传输到下一个节点。当接收到数据时，物理层会将信号转换成“01”串并且将其传输给`Data Link Layer`。`Data Link Layer`会将这些数据帧重新组合在一起。

![](https://s2.loli.net/2022/02/16/h34zmQ1rltjR8yG.png)

**物理层的作用**

* **Bit synchronization（位同步）**：物理层通过提供“时钟（clock）”来实现位同步。该时钟控制发送器和接收器，从而提供位级同步。
* **Bit rate control（位速率控制）**：物理层同时决定了数据的传输速率，即每秒传输的比特数
* **Physical topologies（物理拓扑）**：物理层指定不同的设备/节点在网络中的排列方式，即总线、星形或网状拓扑。
* **Transmission mode（传输方法）**：物理层还定义了数据在两个连接设备之间流动的方式。可能的各种传输模式是单工（simplex）、半双工（half-duplex）和全双工（full-duplex）。

> 集线器（Hub）、中继器（Repeater）、调制解调器（Modem）、电缆（Cables）是物理层设备。



##### 1.4.1.2 数据链路层（Data Link Layer）

数据链路层负责消息的节点到节点传递。该层的主要功能是确保通过物理层从一个节点到另一个节点的数据传输无差错。当数据包到达网络时，数据链路层 负责使用其 MAC（Media Address Control） 地址将其传输到主机。数据链路层可以划分成两个子层：

1. Logical Link Control
2. Media Access Control

![](https://s2.loli.net/2022/02/16/vj6Kw7uTQbe82rx.png)

**数据链路层的作用**

* **Frame（帧）**：成帧（Framing）是数据链路层的功能。它为发送者提供了一种方式来传输对接收者有意义的一组比特。这可以通过在帧的开头和结尾附加特殊的位模式来实现。
* **Physical addressing（物理寻址）**：在创建帧之后，数据链路层在每个帧的头中添加发送方和/或接收方的物理地址（MAC地址）
* **Error control（错误控制）**：数据链路层提供了一种控制错误的机制，它会发现并且重新传输损伤或者丢失的帧。
* **Flow control（流控制）**：双方的数据速率必须是恒定的，否则数据可能会被破坏，因此，流控制会协调在接收到确认之前可以发送的数据量。
* **Access control（权限控制）**：当一个单一的交流通道（a single communication channel）被多个设备共享时，数据链路层的子层 -- MAC会决定哪个设备在哪个时间能够拥有该通道的控制权。

> 数据链路层中的数据包称为**帧（Frame）**
>
> 交换机（Switch）和网桥（Bridge）是数据链路层设备



##### 1.4.1.3 网络层（Network Layer）

网络层用于将数据从一台主机传输到位于不同网络中的另一台主机。它还负责数据包路由，即从可用路由的数量中选择传输数据包的最短路径。发送方和接收方的IP地址由网络层放置在标头中。

![](https://s2.loli.net/2022/02/16/eOz3vB1DN6Jp4kW.png)

**网络层的作用**

* **Routing（路由）**：网络层协议决定了哪条路由适合从源到目的地。

	> 什么是**Routing**：**Routing** is the process of selecting a path for traffic in a [network](https://en.wikipedia.org/wiki/Network_theory) or between or across multiple networks.(wikipedia)

* **Logical Addressing（逻辑寻址）**：为了唯一地识别互联网上的每个设备，网络层定义了一个寻址方案。发送方和接收方的 IP 地址由网络层放置在标头中。这样的地址可以唯一且普遍地区分每个设备。

> 网络层中的数据段（Segment）称为包（Packet）
>
> 网络层由路由器等网络设备实现。



##### 1.4.1.4 传输层（Transport Layer）

> Keyword: Reliability, Flow control, Error correction

传输层向应用层提供服务，并从网络层获取服务。传输层中的数据称为分段（Segment）。它负责完整消息的端到端（End-to-End）交付。传输层还提供数据传输成功的确认，并在发现错误时重新传输数据。

* **在发送方看来：**

	传输层从上层接收格式化的数据，执行分段（Segmentation），并执行**流和错误控制**以确保正确的数据传输。它还在其标头中添加源和目标**端口号**，并将分段数据转发到网络层。

	> note：发送方需要知道与接收方应用程序关联的端口号。 通常，此目标端口号是默认配置或手动配置的。例如，当 Web 应用程序向 Web 服务器发出请求时，它通常使用端口号 80，因为这是分配给 Web 应用程序的默认端口。许多应用程序都分配了默认端口。

* **在接收方看来：**

	传输层从标头读取端口号，并将接收到的数据转发给相应的应用程序。它还执行分段数据的排序和重组。

**传输层的作用**

* **Segmentation and Reassembly（分段与重组）**：该层接受来自（会话）层的消息，将消息分成更小的单元。生成的每个段都有一个与之关联的标题。目标的传输层重新组装消息。
* **Service Point Addressing（服务点寻址）**：为了将消息传递给正确的进程，传输层标头（layer header）包含一种称为服务点地址或端口地址的地址。因此，通过指定此地址，传输层确保将消息传递到正确的位置。

> 传输层由操作系统操作。它是操作系统的一部分，通过系统调用与应用层通信。 传输层被称为 OSI 模型的核心。
>
> 数据在传输层中被称作**段（Segments）**



##### 1.4.1.5 会话层（Session Layer）

会话层负责连接的建立、会话的维护、身份验证，同时也保证了安全性。

**会话层的作用**

* **Session establishment, maintenance, and termination（会话建立、维护和终止）**：会话层允许两个进程建立，使用和终止连接
* **Synchronization（同步）**：该层允许进程将被视为同步点的检查点添加到数据中。这些同步点有助于识别错误，以便正确地重新同步数据，并且不会过早地切断消息的结尾并避免数据丢失。
* **Dialog Controller（对话控制器）**：会话层允许两个系统以半双工（half-duplex）或全双工（full-duplex）方式开始相互通信。



##### 1.4.1.6 表示层（Presentation）

表示层也称为**翻译层（Translation layer）**。来自应用层的数据在这里被提取出来，并按照所需的格式进行处理，以通过网络传输。

**表示层的作用**

* **Translation（翻译）**：比如，`ASCII`翻译成`EBCDIC`
* **Encryption/Decryption（加密/解密）**：数据加密，将数据转换成另一种形式或代码。加密后的数据称为密文，解密后的数据称为纯文本。密钥值用于加密和解密数据。
* **Compression（压缩）**：减少需要在网络上传输的比特数。



##### 1.4.1.7 应用层（Application Layer）

在 OSI  Model的最顶层，就是由网络应用程序实现的应用程序层。这些应用程序产生数据，这些数据必须通过网络传输。该层还用作应用服务访问网络和向用户显示接收到的信息的窗口。

![](https://s2.loli.net/2022/02/16/I6wUEWsukCKcBzS.png)

**应用层的作用**

* 网络虚拟终端
* FTAM-文件传输访问和管理
* 邮件服务
* 目录服务
* ......



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



#### 1.4.3 总结

OSI 模型作为参考模型，由于其发明较晚，并未在 Internet 上实现。当前使用的模型是 TCP/IP 模型。


## Reference

1. 南京大学软件学院2022春季学期《互联网计算》课程
2. [Layers of OSI Model](https://www.geeksforgeeks.org/layers-of-osi-model/)
