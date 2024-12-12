---
title: Java Web
date: 2022-02-24 16:08:03
description: Java Web简单介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku14.jpg
tags:
    - Software Engineering
    - Java Web
categories:
    - Java
    - Web
---



## 0 综述

以`Java`为后端，`Springboot` + `Mybatis`为技术栈的`Web`项目一直是目前十分火热的点，本文将会介绍我在学习过程中总结的一些`Web`知识，目前打算分成以下几篇来写：

1. Java Web -- Web Application, Server and Client

	> 主要讲解Web Application中的一些基本概念

2. Java Web -- Tomcat and Servlet

  > 主要讲解Java中的Servlet和Tomcat，这两者是Java在开发`Server`端时十分重要的两个东西。

3. Java Web -- 分层模型

4. Java Web -- Springboot

	> 主要讲解Springboot，Springboot是在Spring基础上简化配置的一款现代化Web Applicaiton开发框架，具有内置Tomcat，配置简单等优点。

5. Java Web -- Mybatis

	> Mybatis是Java中的一款数据持久化框架，能够在Java实体类和SQL语句间建立映射关系，是一种半自动化的ORM（Object Relational Mapping）实现

<!--more-->


## 1 What is a web application

> A **web application** (or **web app**) is application software that runs on a web server, unlike computer-based software programs that are run locally on the operating system (OS) of the device. Web applications are accessed by the user through a web browser with an active network connection. These applications are programmed using a client–server modeled structure—the user ("*client*") is provided *services* through an *off-site server* that is hosted by a third-party. Examples of commonly-used web applications include: web-mail, online retail sales, online banking, and online auctions. -- from WIKI

简单来说，`Web Application`（以下我都翻译成**Web应用**）就是在浏览器中运行的运用，这些运用不像传统的运行在本地机器上的运用，而是由远端的服务器（`Server`）来提供服务，从而让使用者（`Client`）可以享受到服务。

Web 应用程序通常使用浏览器支持的语言（如 JavaScript 和 HTML）进行编码，因为这些语言依赖于浏览器来呈现程序可执行文件。一些应用程序是动态的，需要服务器端处理。其他是完全静态的，不需要在服务器上进行处理。



## 2 How a web application works

### 2.1 Http请求

`HTTP`（超文本传输协议，HyperText Transfer Protocol）是一种在`Client`和`Server`之间进行沟通的“协议”。

> 前面说到Web应用都不是运行在本地的，也就是有多台机器来进行数据和信息的传输，这就需要约定一些规则和协议来保证数据和信息传输的规范性和有效性。

`HTTP`协议是运行在`TCP/IP`协议上的，`HTTP`请求通常能够分为以下几个组成的部分：

1. **HTTP Method**：HTTP Method通常指定了要执行方法的类型，比如说`GET/POST/PUT/DELETE`等请求，他们有着不同的含义，是`Server`与`Client`之间进行交流传输的一种类似标志的信息，通过这些类型服务端能够判断自己将要返回哪些东西和进行哪些类型的操作。

2. **URL**：URL是在开发Web应用时确定的Web网页的地址，能够通过URL来确定一个网页。

3. **Form Parameters**：表单参数有点像`Java`方法中的参数，它是`Client`为了提供一些具体参数给`Server`，让`Server`来执行具体操作的东西

	> 比如说，登录时提供的用户名和密码。



### 2.2 Web application 的一般工作流程

> 用户指的就是`Client`，服务器指的就是`Server`
>
> Web应用程序服务器和Web服务器之间的差别在于前者是处理具体请求的，后者相当于只是接受用户发送的HTTP请求和传输回去，并不介入具体的数据处理中。
>
> 前者就相当于Java项目，后者就相当于Tomcat。

1. 用户通过浏览器或应用程序的用户界面通过Internet触发对Web服务器的请求
2. Web服务器将此请求转发到响应的Web应用程序服务器
3. Web应用服务器执行请求的任务，对数据库进行增删改查操作，然后生成请求数据的结果
4. Web 应用程序服务器将结果与请求的信息或处理的数据一起发送到 Web 服务器
5. Web 服务器用请求的信息响应客户端，然后显示在用户的显示器上



## 3 Benefits of a web application

1. 只要浏览器兼容，Web 应用程序就可以在多个平台上运行，无论操作系统或设备如何
2. 所有用户访问相同版本，消除任何兼容性问题
3. Web应用没有安装在硬盘上，因此消除了空间限制
4. Web应用减少了基于订阅的 Web 应用程序（即 SaaS）中的软件盗版
5. Web应用降低了业务和最终用户的成本，因为业务所需的支持和维护更少，对最终用户计算机的要求也更低



## Reference

1. [How to build a Web Application Using Java](https://www.javatpoint.com/how-to-build-a-web-application-using-java)
2. [Java Basics: What Is Apache Tomcat](https://www.jrebel.com/blog/what-is-apache-tomcat)
3. [Servlets \| Servlet Tutorial](https://www.javatpoint.com/servlet-tutorial)
4. [What is REST](https://restfulapi.net/)
5. [Java Web 中的三层架构](https://zhuanlan.zhihu.com/p/30832759)
6. [Java Web中的各种概念](https://blog.csdn.net/qq_33589510/article/details/104082803)
7. [What is Web Application -- Wikipedia](https://en.wikipedia.org/wiki/Web_application)


