---
title: Design Pattern (5)
date: 2022-05-28 20:23:39
description: 设计模式中的行为型模式简要介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku43.jpg
tags:
    - SELearning
    - '面向对象'
    - Design Pattern
categories:
    - Design Pattern
---

## 9 策略模式 Strategy Pattern

### 9.1 概述

**策略模式**是为了解决软件开发中存在的多种算法灵活切换的问题，如果使用硬编码（Hard Coding）会导致系统违背开闭原则，扩展性差，且维护困难，这个时候我们可以通过策略模式来定义类封装不同的算法。

**定义**：定义一系列算法，将每一个算法封装起来，并让它们可以相互替换。策略模式让算法可以独立于使用它的客户变化。

> 对象行为型模式

* 又称为政策（Policy）模式
* 每一个封装算法的类称之为**策略（Strategy）类**
* 策略模式提供了一种**可插入式（Pluggable）算法**的实现方案

### 9.2 结构与实现

#### 9.2.1 结构

![image-20220528203731535](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220528203731535.png)

策略模式包含以下3中角色：

* `Context`（环境类）
* `Strategy`（抽象策略类）
* `ConcreteStrategy`（具体策略类）

#### 9.2.2 实现

> 你会发现结构图其实和状态模式相比只是对应的类换了一个名字，这其实是一种面向对象思想在不同使用场景下的具体演化，我们都会通过抽象来将多个类的共同特征抽象为一个抽象类，并通过聚合来将该抽象类与相关对象以成员变量的形式聚合（or 组合）在一起，这样方便后续代码的扩展，如果新增了具体类，只需要实现抽象类的接口就可以完成系统的扩展，而不需要修改原先的代码。

典型的**抽象策略类**代码（也可以声明为接口）

```java
public abstract class Strategy {
    
    /**
     * 声明抽象算法
     */
    public abstract void algorithm();
}
```

典型的**具体策略类**代码

```java
public class ConcreteStrategy extends Strategy {
    
    /**
     * 算法的具体实现
     */
    @Override
    public void algorithm() {
        // 算法A
    }
}
```

典型的**环境类**代码

```java
public class Context {
    
    /**
     * 维持一个对抽象策略类的引用
     */
    private Strategy strategy;
    
    /**
     * setter注入策略对象
     */
    public void setStrategy(Strategy strategy) {
        this.strategy = strategy;
    }
    
    public void algorithm() {
        strategy.algorithm();
    }
}
```

典型的客户端代码片段。

```java
Context context = new Context();
// 可以在运行时指定类型，通过XML配置文件或者反射机制来实现
Strategy strategy = new ConcreteStrategyA();

context.setStrategy(strategy);
context.algorithm();
```



### 9.3 实例

### 9.4 AWT + Swing中的布局管理

Java GUI编程中的 `LayoutManager` 其实就用到了策略模式来实现对不同布局的应用，通过将不同布局封装为不同的 **策略类**，用户在使用时只需要通过 `setter` 注入想要使用的布局就可以获得想要的效果。

![image-20220528204611039](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220528204611039.png)



### 9.5 优缺点与适用环境

#### 9.5.1 优点

* 提供了对开闭原则的完美支持，用户可以在不修改原有系统的基础上选择算法或行为，也可以灵活地增加新的算法或行为
* 提供了管理相关的算法族的办法
* 提供了一种可以替换继承关系的办法
* 可以避免多重条件选择语句
* 提供了一种算法的复用机制，不同的环境类可以方便地复用策略类

#### 9.5.2 缺点

* 客户端必须知道所有的策略类，并自行决定使用哪一个策略类
* 将造成系统产生很多具体策略类
* 无法同时在客户端使用多个策略类

#### 9.5.3 适用环境

* 一个系统需要动态地在几种算法中选择一种
* 避免使用难以维护的多重条件选择语句
* 不希望客户端知道复杂的、与算法相关的数据结构，提高算法的保密性与安全性



## Reference

1. Java设计模式 -- 刘伟，清华大学出版社
2. 面向对象设计方法 -- 南京大学计算机科学与技术系2022春季课程
3. Design Pattern -- GoF 23种设计模式
