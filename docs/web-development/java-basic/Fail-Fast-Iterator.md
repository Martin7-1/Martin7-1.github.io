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
    - Advanced
---

## 前言

快速失败（Fail-Fast）机制是一种在多线程写入或更新情况下的应对机制，能够在不处理后续操作的情况下先行抛出异常以便后续的处理。为什么会突然来学习快速失败机制呢，主要是在看 `ArrayList` 的源码的时候看到其有一个成员变量：`protected transient int modCount = 0`，好奇这个变量是做什么用的，查看了它的调用方法发现其是用于在检查多线程和迭代器写入，于是就去查阅文档和资料。

<!-- more -->

![image-20220531232146974](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220531232146974.png)

## 概述

Fail-Fast是用在一个 `Collection` 类型（[Java Collection Framework](https://docs.oracle.com/javase/8/docs/technotes/guides/collections/overview.html)）遍历时修改自身或者多线程并发修改一个 `Collection` 类型的情况下出现的现象，对于不同的 `Collection` 类型，可能会有不同的处理并发修改的机制。

> `Collection` 类型包括的很广，比如熟悉的 `ArrayList` 、`LinkedList` 、`HashMap` 等，也有多线程场景下的 `CopyOnWriteArrayList` 和 `ConcurrentHashMap` 等，不同的 `Collection` 对并发修改实现不同，体现了面向对象中的多态。需要注意的是，这里的并发修改是指直接在遍历时是集合类型进行修改，而不是通过 `iterator` 来修改（部分 `Collection` 通过 `iterator` 来修改自身可能有问题，部分可能是没问题的）。

与 **Fail-Fast** 相对的是 **Fail-Safe**，但官方文档中并未出现一次，而是描述了一下四种对于并发情况下的不同策略：

1. **Fail-Fast**
2. **Weakly Consistent**
3. **Snapshot**
4. **Undefined**

## 并发操作

Java 中的并发修改是在另一个任务在其上运行时同时修改一个对象。简单来说，并发修改是在另一个线程运行对象时修改对象的过程。它将通过删除、添加或更新集合中元素的值来改变数据集合的结构。 并非所有迭代器都支持这种行为；某些迭代器的实现可能会抛出 `ConcurrentModificationException`。

## Fail-Fast

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



## Weakly-Consistent

这一类主要指的是在 `java.util.concurrent` 包下的一些 `Collection` 的拓展类，一般是在类前面包括 `concurrent` 的前缀，比如：

* `ConcurrentHashMap` 代表的是线程安全的 `HashMap`（文档中的准确表述为 a `ConcurrentHashMap` is normally preferable to a synchronized `HashMap`）
* `ConcurrentSkipListMap` 代表的hi线程安全的 `TreeMap`

`concurrent` 代表的是同时，在文档中解释了此类与 **Fail-Fast** 机制的差别以及 `concurrent` 的意思：

> Most concurrent Collection implementations (including most Queues) also differ from the usual `java.util` conventions in that their [Iterators](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html) and [Spliterators](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html) provide *weakly consistent* rather than fast-fail traversal:
>
> - they may proceed concurrently with other operations
> - they will never throw [`ConcurrentModificationException`](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html)
> - they are guaranteed to traverse elements as they existed upon construction exactly once, and may (but are not guaranteed to) reflect any modifications subsequent to construction.

这里需要探究的是最后一点，以 `ConcurrentHashMap` 举例，我们有如下代码：

```java
import java.util.concurrent.ConcurrentHashMap;   
import java.util.Iterator;

public class FailSafeDemo1 {
    
    public static void main(String[] args)   
    {   
        // Initializing a ConcurrentHashMap   
        ConcurrentHashMap<String, Integer> m = new ConcurrentHashMap<>();   
        m.put("ONE", 1); // Adding values  
        m.put("SEVEN", 7);   
        m.put("FIVE", 5);   
        m.put("EIGHT", 8);   
        // Getting an iterator using map  
        Iterator it = m.keySet().iterator();   
        while (it.hasNext()) {   
            String key = (String)it.next();   
            System.out.println(key + " : " + m.get(key));   
            // This will reflect in iterator.   
            // This means it has not created separate copy of objects  
            m.put("NINE", 9);   
        }   
    }   
}  
```

Output:

```bash
EIGHT : 8
FIVE : 5
NINE : 9
ONE : 1
SEVEN : 7
```

我们可以看到，在使用迭代器遍历时我们调用了 `ConcurrentHashMap` 的 `put()` 方法，但这并没有和 `ArrayList` 的 **Fail-Fast** 机制一样快速抛出 `ConcurrentModificationException` 异常且停止一切操作，相反，它正确的加入了该元素且并没有抛出任何异常。

这里我们不像 `ArrayList` 一样去探究 `ConcurrentHashMap` 的源码（主要是因为太难了我看不懂），但在 `ConcurrentHashMap` 的 `put()` 方法中，我们可以看到以下的代码块：

```java
synchronized (f) {
    if (tabAt(tab, i) == f) {
        if (fh >= 0) {
            binCount = 1;
            for (Node<K,V> e = f;; ++binCount) {
                K ek;
                if (e.hash == hash && ((ek = e.key) == key || (ek != null && key.equals(ek)))) {
                    oldVal = e.val;
                    if (!onlyIfAbsent)
                        e.val = value;
                    break;
                }
                Node<K,V> pred = e;
                if ((e = e.next) == null) {
                    pred.next = new Node<K,V>(hash, key, value, null);
                    break;
                }
            }
        }
        else if (f instanceof TreeBin) {
            Node<K,V> p;
            binCount = 2;
            if ((p = ((TreeBin<K,V>)f).putTreeVal(hash, key, value)) != null) {
                oldVal = p.val;
                if (!onlyIfAbsent)
                    p.val = value;
            }
        }
    }
}
```

可以看到这里对 `f` 加了锁，这个 `f` 是 `HashMap` 中的一个 `Node` ，这里实际上就是为支持并发的操作写入而加了锁。



## Snapshot

快照，顾名思义，就是保存某一时刻瞬间的状态，反应在迭代器上就是我们在使用迭代器的时候其实获得的是当前 `Collection` 的副本，因此对原 `Collection` 进行操作实际上无法变成对副本进行操作，且不会抛出 `ConcurrentModificationExpection`，但是修改后的结果对**迭代器**来说是不可见的，这一类的代表是 `CopyOnWriteArrayList`，是一种线程安全的 `ArrayList`。在文档中是这样描述的：

> This is ordinarily too costly, but may be *more* efficient than alternatives when traversal operations vastly outnumber mutations, and is useful when you cannot or don't want to synchronize traversals, yet need to preclude interference among concurrent threads. The "snapshot" style iterator method uses a reference to the state of the array at the point that the iterator was created. This array never changes during the lifetime of the iterator, so interference is impossible and the iterator is guaranteed not to throw `ConcurrentModificationException`. The iterator will not reflect additions, removals, or changes to the list since the iterator was created. Element-changing operations on iterators themselves (`remove`, `set`, and `add`) are not supported. These methods throw `UnsupportedOperationException`.

简单来说就是迭代器是通过创建一个底层数组的**副本**来进行遍历，所有对原数组的操作实际上不会影响到这个副本，即迭代器的元素本身迭代时是不会因为 `CopyOnWriteArrayList` 的方法而被改变的，我们可以通过以下的例子来看一下：

```java
import java.util.concurrent.CopyOnWriteArrayList;   
import java.util.Iterator;   

class FailSafeDemo {
    
    public static void main(String[] args)
    {
        CopyOnWriteArrayList<Integer> list = new CopyOnWriteArrayList<>(Arrays.asList(1, 7, 9, 11));
        Iterator<Integer> itr = list.iterator();
        while (itr.hasNext()) {
            Integer i = itr.next();
            System.out.println(i);
            if (i == 7) {
                list.add(15); // It will not be printed
            }
            //This means it has created a separate copy of the collection
        }
        
        System.out.println();
        
        for (Integer value : list) {
            System.out.println(value);
        }
    }
}   
```

Output:

```bash
1
7
9
11

1
7
9
11
15
```

可以发现15并没有被添加到迭代器的元素中，而是被添加到了原来的数组中，我们可以看看 `CopyOnWriteArrayList` 迭代器的实现：

```java
public Iterator<E> iterator() {
    return new COWIterator<E>(getArray(), 0);
}

static final class COWIterator<E> implements ListIterator<E> {
    /** Snapshot of the array */
    private final Object[] snapshot;
    /** Index of element to be returned by subsequent call to next.  */
    private int cursor;

    private COWIterator(Object[] elements, int initialCursor) {
        cursor = initialCursor;
        snapshot = elements;
    }
    
    ...
}
```

可以发现构造迭代器时实际上是将当前 `CopyOnWriteArrayList` 的底层数组作为参数传递进行了一次拷贝，但有可能会问，这里拷贝的是**引用**，对原数组的更改应该要能够影响到所有的引用才对，为什么迭代器中的数组元素不会改变呢。我们可以来看一下 `CopyOnWriteArrayList` 的 `add()` 方法：

```java
public boolean add(E e) {
    final ReentrantLock lock = this.lock;
    lock.lock();
    try {
        Object[] elements = getArray();
        int len = elements.length;
        Object[] newElements = Arrays.copyOf(elements, len + 1);
        newElements[len] = e;
        setArray(newElements);
        return true;
    } finally {
        lock.unlock();
    }
}
```

可以看到 `add()` 方法对原数组做了一次拷贝之后，添加了新的元素，然后赋值给了底层的数组，这样就导致 `add()` 方法之后，迭代器和原先 `CopyOnWriteArrayList` 的底层数组的**引用指向的堆上的对象不同**了，因此就导致了元素的差异。那么可能有人问，如果通过迭代器的 `add()` 、`remove()` 方法来修改呢？上面的文档其实已经提到了，迭代器对于这类操作一律会抛出 `UnsupportedOperationException` 来保证数据遍历时的不变性。

这其实也是线程安全的数据一致性体现，当前时刻获取的数据就要一直保持是当前时刻的数据，而不能随着后续数据的增加而一直变动，这样会造成一些不可预知的问题，用户想要获得当前时刻的数据就只要通过迭代器获得拷贝的副本，就可以遍历当前时刻的数据了，同时，对于多个迭代器，为了防止相同引用之间不互相干扰和修改数据， `CopyOnWriteList` 杜绝了迭代器修改元素的可能性，做到了线程之间的隔离和安全。当然，所需的内存相比线程不安全的 `Collection` 也是有所提升的（毕竟进行了数组的拷贝）。

## Undefined

这一类就是没有指定在使用迭代器的时候修改原先 `Collection` 的元素会发生什么，未定义行为可能会导致不一致的结果，此类的代表通常是 `Vector`、`HashTable` 等。（`Enumeration` 是 **undefined** 的，`iterator` 是 **Fail-Fast** 的）。

## 总结

迭代器模式是一种设计中常用的模式，通过迭代器模式可以实现元素的获得和更改之间的职责解耦，同时通过迭代器，用户可以专心于对其中元素进行操作，而不用关心是如何实现获得元素。**Fail-Fast** 和其他机制下实现的迭代器适用于不同的场景，需要根据不同的场景来进行选择。

## Reference

1. [Fail Fast and Fail Safe Iterator in Java](https://www.javatpoint.com/fail-fast-and-fail-safe-iterator-in-java#:~:text=The%20Java%20Collection%20supports%20two%20types%20of%20iterators%3B,it%20exposes%20failures%20and%20stops%20the%20entire%20operation.)
2. [请不要再说了fail-safe了，Java里根本没有这回事](https://zhuanlan.zhihu.com/p/300162795)
3. [java.util.concurrent package specification](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#Weakly)
4. [java.util.ArrayList specification](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html#fail-fast)
5. [java.util.concurrent.CopyOnWriteArrayList specification](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CopyOnWriteArrayList.html)