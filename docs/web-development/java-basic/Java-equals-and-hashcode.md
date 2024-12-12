---
title: Java equals() and hashcode()
date: 2022-09-24 18:51:49
description: Java 中 equals() 方法和 hashcode() 方法的简单介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku76.jpeg
tags:
    - Java
categories:
    - Java
    - Basic
---

## `equals()` 的性质

1. Refleive（自反性）：对于任意一个对象 a，`a.euals(a)` 总是返回 `true`
2. Symmetric（对称性）：对于任意两个对象 a，b，如果 `a.equals(b)` 返回 `true`，那么 `b.equals(a)` 一定也会返回 `true`
3. Transitive（传递性）：对于任意三个对象 a，b，c：如果 `a.equals(b)` 和 `b.equals(c)` 都返回 `true`，那么 `a.equals(c)` 一定也会返回 `true`
4. Consistent（持续性）：对于任意两个对象 a，b，多次调用 `a.equals(b)` 返回的都是 `true` 或者返回的都是 `false`（即多次调用都只会返回相同的结果，前提是没有修改过 a 或 b）

<!-- more -->

> Note: 对于任意非 null 的对象 a，`a.equals(null)` 返回的都是 false，如果 a 是 null，那么调用 `a.equals()` 会返回 `NullPointerException`。因此要注意的是在使用 `equals()` 方法的时候应该要优先让非 null 的对象作为调用方（比如一些常量、枚举等）。

## `equals()` 的作用是什么

`equals()` 的作用是用来判断两个对象是否相等，该方法定义在 Object 类中，通过比较两个对象的地址是否相等（即该引用是否指向了同一个对象），源码如下：

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

既然 `Object` 中定义了 `equals()` 方法，这就意味着所有的Java类都实现了 `equals()` 方法，所有的类都可以通过 `equals()` 去比较两个对象是否相等。 但是，我们已经说过，使用默认的 `equals()` 方法，等价于“==”方法。因此，我们通常会重写 `equals()` 方法：若两个对象的内容相等，则 `equals()` 方法返回 `true`；否则，返回 `false`。

我们可以根据是否重写（override）了 `equals()` 方法来将该方法分为两类：

1. 如果某个类没有重写 `equals()` 方法，当它通过 equals() 来比较两个对象的时候，实际上是在比较两个对象**是否为同一个对象**
2. 如果某个类重写了 `equals()` 方法，可以通过重写来让 `equals()` 通过其他方式（比如比较两个对象之间的属性值是否相等）来比较两个对象是否相等。

下面通过代码来对是否重写了 `equals()` 方法的区别来做一个对比

**没有重写 `equals()` 方法**：

```java
@AllArgsConstructor
@NoArgsConstructor
public class Student {

    private String name;
    private int sex;


    public static void main(String[] args) {
        Person personOne = new Person("test", 100);
        Person personTwo = new Person("test", 100);

        System.out.println(personOne.equals(personTwo));
    }
}
```

**运行结果**

```bash
false
```

**重写了 `equals()` 方法**

```java
@AllArgsConstructor
@NoArgsConstructor
public class Person {

    private String name;
    private int sex;

    @Override
    public boolean equals(Object obj) {
        // 检查是否为同一个对象
        if (obj == this) {
            return true;
        }

        // 检查 obj 是否为 null，且是否和该对象是相同的类型
        if (obj == null || obj.getClass() != this.getClass()) {
            return false;
        }

        Person person = (Person) obj;
        return (person.name.equals(this.name)) && (person.sex == this.sex);
    }

    @Override
    public int hashcode() {
        // todo: return hashcode
        return sex;
    }

    public static void main(String[] args) {
        Person personOne = new Person("test", 100);
        Person personTwo = new Person("test", 100);

        System.out.println(personOne.equals(personTwo));
    }
}
```

**运行结果**

```bash
true
```

可以看到，重写了 `equals()` 方法的类能够使用 `equals()` 方法来对两个内容相同的对象返回 `true`。

> Note: `obj.getClass() != this.getClass()` 是否可以通过 `obj instanceOf Person` 代替？
> 
> 答案是不行，对于 `instanceOf` 来说，只要是 `Person` 的子类都会返回 `true`，但是对于 `obj.getClass() != this.getClass()` 来说，才能够判断两个对象的类型是否真正相等。当然根据你的目的来进行判断，看是否允许不同类型通过 `equals()` 方法的重写来返回 `true`

## `equals()` 与 == 的区别是什么

1. `==`: 它的作用是判断**两个对象的地址**是不是相等。即，判断两个对象是不是同一个对象。
2. `equals()`: 它的作用也是**判断两个对象**是否相等。但它一般有两种使用情况：
   1. 类没有覆盖 `equals()` 方法。则通过 `equals()` 比较该类的两个对象时，等价于通过“==”比较这两个对象。
   2. 类覆盖了 `equals()` 方法。一般，我们都覆盖 `equals()` 方法来判断两个对象的内容是否相等；若它们的内容相等，则返回 `true`(即，认为这两个对象相等)。

## `hashcode()` 的作用是什么

`hashCode()` 的作用是获取哈希码，也称为散列码；它实际上是返回一个int整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。`hashCode()` 定义在 `Object` 类中，这就意味着Java中的任何类都包含有 `hashCode()` 函数。

虽然，每个Java类都包含 `hashCode()` 方法。但是，仅仅当创建某个“类的散列表”时，该类的 `hashCode()` 才有用，作用是：确定该类的每一个对象在散列表中的位置；其它情况下(例如，创建类的单个对象，或者创建类的对象数组等等)，类的hashCode() 没有作用。

上面的散列表，指的是：Java集合中本质是散列表的类，如 `HashMap`，`Hashtable`，`HashSet`。散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

> 下面内容摘自 《Head First Java》
> 
> 当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashCode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashCode 值作比较，如果没有相符的 hashCode，HashSet 会假设对象没有重复出现。但是如果发现有相同 hashCode 值的对象，这时会调用 equals() 方法来检查 hashCode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

> Note: `Object` 的 `hashCode()` 方法是本地（native）方法，也就是用 C 语言或 C++ 实现的，该方法通常用来将对象的内存地址转换为整数之后返回。

## `hashcode()` 和 `equals()` 之间有什么联系

### 为什么同时提供了这两种方法

这是因为在一些容器（比如 HashMap、HashSet）中，有了 hashCode() 之后，判断元素是否在对应容器中的效率会更高（参考添加元素进HashSet的过程）！

我们在前面也提到了添加元素进HashSet的过程，如果 HashSet 在对比的时候，同样的 hashCode 有多个对象，它会继续使用 `equals()` 来判断是否真的相同。也就是说 hashCode 帮助我们大大缩小了查找成本。

### 那为什么不只提供 `hashCode()` 方法呢？

这是因为两个对象的hashCode 值相等并不代表两个对象就相等。

### 为什么两个对象有相同的 hashCode 值，它们也不一定是相等的？

因为 hashCode() 所使用的哈希算法也许刚好会让多个对象传回相同的哈希值。越糟糕的哈希算法越容易碰撞，但这也与数据值域分布的特性有关（所谓哈希碰撞也就是指的是不同的对象得到相同的 hashCode）。

总结下来就是 ：

1. 如果两个对象的 hashCode 值相等，那这两个对象不一定相等（哈希碰撞）。
2. 如果两个对象的 hashCode 值相等并且equals()方法也返回 true，我们才认为这两个对象相等。
3. 如果两个对象的 hashCode 值不相等，我们就可以直接认为这两个对象不相等。

### 为什么重写 equals() 时必须重写 hashCode() 方法？

因为两个相等的对象的 hashCode 值必须是相等。也就是说如果 equals 方法判断两个对象是相等的，那这两个对象的 hashCode 值也要相等。

如果重写 equals() 时没有重写 hashCode() 方法的话就可能会导致 equals 方法判断是相等的两个对象，hashCode 值却不相等。

## Reference

1. [Java 基础常见面试题总结](https://javaguide.cn/java/basis/java-basic-questions-02.html#object)
2. [equals() and hashcode()](https://www.geeksforgeeks.org/equals-hashcode-methods-java/)
3. [Java hashcode() 和 equals() 的若干问题解答](https://www.cnblogs.com/skywang12345/p/3324958.html)
