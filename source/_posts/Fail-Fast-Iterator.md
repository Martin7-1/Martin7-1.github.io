---
title: Fail-Fast Iterator in Java
date: 2022-06-01 00:22:25
description: Java Fail-Fast 机制简单介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku45.jpg
tags:
    - Java
    - Concurrent
categories:
    - Java
---

# Fail-Fast Iterator in Java

## 1 前言

快速失败（Fail-Fast）机制是一种在多线程写入或更新情况下的应对机制，能够在不处理后续操作的情况下先行抛出异常以便后续的处理。为什么会突然来学习快速失败机制呢，主要是在看 `ArrayList` 的源码的时候看到其有一个成员变量：`protected transient int modCount = 0`，好奇这个变量是做什么用的，查看了它的调用方法发现其是用于在检查多线程和迭代器写入，于是就去查阅文档和资料。

![image-20220531232146974](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220531232146974.png)

<!-- more -->

## 2 概述

Fail-Fast是用在一个 `Collection` 类型（[Java Collection Framework](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)）遍历时修改自身或者多线程并发修改一个 `Collection` 类型的情况下出现的现象，对于不同的 `Collection` 类型，可能会有不同的处理并发修改的机制。

> `Collection` 类型包括的很广，比如熟悉的 `ArrayList` 、`LinkedList` 、`HashMap` 等，也有多线程场景下的 `CopyOnWriteArrayList` 和 `ConcurrentHashMap` 等，不同的 `Collection` 对并发修改实现不同，体现了面向对象中的多态。需要注意的是，这里的并发修改是指直接在遍历时是集合类型进行修改，而不是通过 `iterator` 来修改（部分 `Collection` 通过 `iterator` 来修改自身可能有问题，部分可能是没问题的）。

与 **Fail-Fast** 相对的是 **Fail-Safe**，但官方文档中并未出现一次，而是描述了一下四种对于并发情况下的不同策略：

1. **Fail-Fast**
2. **Weakly Consistent**
3. **Snapshot**
4. **Undefined**



## 3 并发操作

Java 中的并发修改是在另一个任务在其上运行时同时修改一个对象。简单来说，并发修改是在另一个线程运行对象时修改对象的过程。它将通过删除、添加或更新集合中元素的值来改变数据集合的结构。 并非所有迭代器都支持这种行为；某些迭代器的实现可能会抛出 `ConcurrentModificationException`。



## 4 Fail-Fast

**Fail-Fast**，顾名思义，就是在遇到不正确行为的时候，不执行任何行为就快速的抛出异常，快速失败机制在 Java 中的典型代表主要有 `ArrayList` 和 `HashMap` 等线程不安全的 `Collection`。在 [ArrayList Specification](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html#fail-fast) 中是这么描述的：

> 此类的 `iterator` 和 `listIterator` 方法返回的迭代器是快速失败的：如果在创建迭代器后的任何时间对列表进行结构修改，除了通过迭代器自己的 `remove` 或 `add` 方法之外的任何方式，迭代器将抛出 `ConcurrentModificationException`。因此，面对并发修改，迭代器快速而干净地失败，而不是在未来不确定的时间冒任意的、非确定性的行为。

简单来说，**Fail-Fast**提供了一种在多线程以及遍历时修改自身的快速抛出异常机制，能够在不执行本来应该执行的插入 or 删除等操作，而直接抛出异常，有助于在异常发生时维护数据的一致性和不变性，我们通过 `HashMap` 来看一个例子（例子来源：[Fail Fast and Fail Safe Iterator in Java](https://www.javatpoint.com/fail-fast-and-fail-safe-iterator-in-java#:~:text=The%20Java%20Collection%20supports%20two%20types%20of%20iterators%3B,it%20exposes%20failures%20and%20stops%20the%20entire%20operation.)）：

```java
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;

public class FailFastDemo {
    
    public static void main(String[] args) {
        Map<String, String> empName = new HashMap<>();
        
        empName.put("Sam Hanks", "New York");
        empName.put("Will Smith", "LA");
        empName.put("Scarlett", "Chicago");
        
        Iterator iter = empName.keySet().iterator();
        while (iterator.hasNext()) {
            System.out.println(empName.get(iterator.next()));
            
            // adding an element to Map
            // Exception will be thrown on next call of next() method
            empName.put("Istanbul", "Turkey");
        }
    }
}
```

Output：

```bash
LA
Exception in thread "main" java.util.ConcurrentModificationException
	at java.util.HashMap$HashIterator.nextNode(HashMap.java:1445)
	at java.util.HashMap$KeyIterator.next(HashMap.java:1469)
	at FailFastDemo.main(FailFastDemo.java:14)
```

从这个例子中我们知道了以下几点：

* **Fail-Fast** 的迭代器会在我们使用迭代器遍历时操作该集合时抛出 `ConcurrentModificationException` 异常
* **Fail-Fast** 迭代器操作的是**原始**（请留意这个词，后面我们会有别的不同实现以做对比）的数据



源码中的抛出异常是这样写的（以 `ArrayList` 为例子）：

```java
public E next() {
    checkForComodification();
    int i = cursor;
    if (i >= size)
        throw new NoSuchElementException();
    Object[] elementData = ArrayList.this.elementData;
    if (i >= elementData.length)
        throw new ConcurrentModificationException();
    cursor = i + 1;
    return (E) elementData[lastRet = i];
}

final void checkForComodification() {
    if (modCount != expectedModCount)
        throw new ConcurrentModificationException();
}
```

> 其中，`expectModCount` 是在返回迭代器（即初始化迭代器，调用 `iterator()` 方法会返回一个全新的迭代器）时设置为 `expectModCount = modCount`，如果我们在获得迭代器之后通过**集合本身**（注意是集合本身而不是使用迭代器的）的 `add` 或者 `remove` 方法来修改集合，那么集合的 `add` 或者 `remove` 方法就会讲 `modCount++`，导致 `expectModCount != modCount`，进而抛出异常。

`ArrayList` 本身的 `add` 方法

```java
public void add(int index, E element) {
    rangeCheckForAdd(index);

    ensureCapacityInternal(size + 1);  // Increments modCount!!
    System.arraycopy(elementData, index, elementData, index + 1, size - index);
    elementData[index] = element;
    size++;
}

private void ensureCapacityInternal(int minCapacity) {
    ensureExplicitCapacity(calculateCapacity(elementData, minCapacity));
}

private void ensureExplicitCapacity(int minCapacity) {
    modCount++;

    // overflow-conscious code
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
```

可以看到对 `modCount` 进行了修改，这样就导致了 `modCount` 和 `expectModCount` 的不一致，从而抛出异常。那么为什么使用迭代器自身的方法来修改不会导致异常呢？我们可以来看一下 `ArrayList` 的 `Iterator` 中实现的 `remove` 方法

```java
public void remove() {
    if (lastRet < 0)
        throw new IllegalStateException();
    checkForComodification();

    try {
        ArrayList.this.remove(lastRet);
        cursor = lastRet;
        lastRet = -1;
        expectedModCount = modCount;
    } catch (IndexOutOfBoundsException ex) {
        throw new ConcurrentModificationException();
    }
}
```

可以看到，在删除之后迭代器会更新 `expectModCount` 的值，从而保证不会抛出异常。



未完待续...



## Reference

1. [Fail Fast and Fail Safe Iterator in Java](https://www.javatpoint.com/fail-fast-and-fail-safe-iterator-in-java#:~:text=The%20Java%20Collection%20supports%20two%20types%20of%20iterators%3B,it%20exposes%20failures%20and%20stops%20the%20entire%20operation.)
2. [请不要再说了fail-safe了，Java里根本没有这回事](https://zhuanlan.zhihu.com/p/300162795)
3. [java.util.concurrent package specification](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#Weakly)
4. [java.util.ArrayList specification](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html#fail-fast)
5. [java.util.concurrent.CopyOnWriteArrayList](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CopyOnWriteArrayList.html)