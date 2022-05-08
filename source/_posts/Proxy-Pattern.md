---
title: Proxy Pattern
date: 2022-02-27 16:08:03
description: 设计模式中的代理模式的简要介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku13.jpg
tags:
    - SELearning
    - Design Pattern
categories:
    - Design Pattern
---

> Purpose: Controls and manage access to the object they are protecting

代理模式是用某个类来代理一个实体对象的行为的一种模式，其目的是为了保护和管理对这个实体对象的访问。

![](https://s2.loli.net/2022/01/29/sOCuFzAb7Q1kwpR.png)

同时，代理模式有可能会在代理类中添加一些附属功能来扩展我们对实体对象（Server）的需求与访问。我们一般采用在代理类中与实体对象**组合**来实现代理模式

<!--more-->



## 1 什么时候需要使用代理模式？

当我们需要创建一个包装器（wrapper）来覆盖客户端的主要对象的复杂性时，我们可以使用代理模式。主要解决在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因（比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问），直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。代理模式分为以下几种类型：



### 1.1 远程代理

他们负责表示位于远程的对象。与真实对象交谈可能涉及数据的编组和解组以及与远程对象的交谈。所有这些逻辑都封装在这些代理中，客户端应用程序无需担心它们。



### 1.2 虚拟代理

如果真实对象需要一些时间来产生结果，这些代理将提供一些默认和即时结果。这些代理启动对真实对象的操作并向应用程序提供默认结果。一旦真实对象完成，这些代理将实际数据推送到它之前提供了虚拟数据的客户端。



### 1.3 保护代理

如果应用程序无权访问某些资源，则此类代理将与有权访问该资源的应用程序中的对象对话，然后返回结果。



### 1.4 智能代理

智能代理通过在访问对象时插入特定操作来提供额外的安全层。一个例子可以是在访问之前检查真实对象是否被锁定，以确保没有其他对象可以更改它。



## 2 例子

### 2.1 静态代理

> 以下的例子称为静态代理

我们将创建一个 `Image` 接口和实现了 `Image` 接口的实体类。`ProxyImage` 是一个代理类，减少 `RealImage` 对象加载的内存占用。

`ProxyPatternDemo` 类使用 `ProxyImage` 来获取要加载的 `Image` 对象，并按照需求进行显示。



![](https://s2.loli.net/2022/01/29/FA1aQ9xsVYyPTMz.png)



创建`Image`接口

```java
public interface Image {
    void display();
}
```

创建接口的实现类，也就是实体对象

```java
public class RealImage implements Image {
 
    private String fileName;
 
    public RealImage(String fileName){
       this.fileName = fileName;
       loadFromDisk(fileName);
    }
 
    @Override
    public void display() {
       System.out.println("Displaying " + fileName);
    }
 
    private void loadFromDisk(String fileName){
       System.out.println("Loading " + fileName);
    }
}
```

创建代理类

```java
public class ProxyImage implements Image{
    
    private RealImage realImage;
    private String fileName;
 
    public ProxyImage(String fileName){
       this.fileName = fileName;
    }
 
    @Override
    public void display() {
       if(realImage == null){
          realImage = new RealImage(fileName);
       }
       realImage.display();
    }
}
```



使用代理对象来获取真正的对象

```java
public class ProxyPatternDemo {
   
    public static void main(String[] args) {
       Image image = new ProxyImage("test_10mb.jpg");
 
       // 图像将从磁盘加载
       image.display(); 
    }
}
```



可能在第一眼看到这个例子的时候，大家会觉得`ProxyImage`完全是冗余的代码，我们不妨考虑这样一个场景：在经过了业务发布多年之后，甲方突然想要更改某项需求，想要你在`image.display()`之后添加一点功能，这时候可能你想的是在原有的`RealImage`基础上进行更改，但这样无法避免的会带来程序的风险性，因为我们知道，我们是无法证明修改之后你的程序能够和修改前一样正确的运行的（程序的正确性证明），所以你只能再一次通过复杂的测试来确保你修改后的代码的逻辑可靠性。

除此之外，可能我们只是在某个地方更改了这个需求，而`RealImage`在很多地方都有使用，其他地方其实并不需要增加这个需求，这个时候如果你单纯的修改`RealImage`，就与需求不符了，此时**代理模式**的作用就显现出来了，我们可以通过与`RealImage`一样实现了`Image`接口的代理类，来添加我们所需要的功能，这样做就可以在减小耦合度的情况下做到职责单一，且符合我们需求的变更，同时又不更改我们原本的业务逻辑。我们也做到了代码的可拓展性，只要我们实现了统一的接口，业务逻辑就能够按照我们的需求来不断的拓展，比方说我们可以实现如下的一个代理类：

```java
public class ResizeImage implements Image {
    private RealImage realImage;
    private String fileName;
 
    public ResizeImage(String fileName){
       this.fileName = fileName;
    }
    
    public ResizeImage(RealImage image) {
        this.realImage = image;
    }
 
    @Override
    public void display() {
       if(realImage == null){
          realImage = new RealImage(fileName);
       }
       realImage.display();
    }
    
    /**
     * 输出一下照片的名字 
     */
    private void log() {
        System.out.println("[Image name]: " + realImage.getName());
    }
}
```

这样就可以做到在业务逻辑扩展的基础上实现职责的单一化和可拓展性，同时降低我们代码的耦合度。



### 2.2 动态代理

动态代理和静态代理的相似处和区别主要有以下几点:

1. 动态代理和静态代理角色一样
2. 动态代理的代理类是动态生成的，不是我们直接写好的
3. 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
	* 基于接口：JDK
	* 基于类：cglib
	* Java字节码实现：javasist

> 了解Java中动态代理的内容，您需要了解：[`Proxy`类](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/Proxy.html)和[`InvocationHandler`类](https://docs.oracle.com/javase/8/docs/api/java/lang/reflect/InvocationHandler.html)。



#### 2.2.1 InvocationHandler

![](https://s2.loli.net/2022/02/27/BZoChVAW42gJ1KN.png)



#### 2.2.2 Proxy

![](https://s2.loli.net/2022/02/27/BZoChVAW42gJ1KN.png)



用以下例子来说明动态代理的一个过程：假设我们现在要实现与一个租房的业务逻辑，我们有业务对象`Client`，有`Landlord`（房东），有一个接口`Rentable`

```java
public interface Rentable {
    void rent();
}
```

```java
public class Landlord implements Rentable {
    
    @Override
    public void rent() {
        System.out.println("I have a house to rent");
    }
}
```



此时我们需要实现`InvocationHandler`接口来为我们自动生成代理程序

```java
public class ProxyInvocationHandler<T> implements InvocationHandler {

    private T rentable;

    @SuppressWarnings("unchecked")
    public T getProxy() {
        // 这里会得到自动生成的代理对象，运用了泛型，或者可以返回Object来强制转换
        return (T) Proxy.newProxyInstance(this.getClass().getClassLoader(), rentable.getClass().getInterfaces(), this);
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

        // 动态代理的本质是使用反射来实现
        Object result = method.invoke(rentable, args);

        return result;
    }
}
```



在`Client`中我们要调用业务逻辑则可以这样来写：

```java
public class Client {

    public static void main(String[] args) {
        Host host = new Host();

        ProxyInvocationHandler<Rentable> proxyInvocationHandler = new ProxyInvocationHandler<>();
        proxyInvocationHandler.setRentable(host);
        Rentable proxy = proxyInvocationHandler.getProxy();

        proxy.rent();
    }
}
```



此时我们如果要添加业务逻辑，只需要在`Handler`的`invoke`方法中调用我们需要增加的业务逻辑方法即可。



## 3 代理模式的某些应用

1. `SpringAOP`的底层实现实际上就是jdk的动态代理和cglib动态代理技术
2. Windows下的快捷方式



## 4 代理模式的优点

1. 职责清晰
2. 高扩展性
3. 智能化



## 5 代理模式的缺点

1. 由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。
2. 实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

