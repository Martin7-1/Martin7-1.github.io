---
title: Java String 深入理解
date: 2022-09-24 20:57:44
description: 深入解析 Java 中 String
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku77.jpeg
tags:
    - Java
categories:
    - Java
    - Advanced
---

## `String`、`StringBuffer`、`StringBuilder` 的区别

### 可变性

1. `String` 是不可变的（详细原因后面分析）
2. `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在该类中也是使用 `char` 数组来保存字符串（JDK9 之后改为了使用 `byte` 数组，后面会介绍），对于这个数组，在 `AbstractStringBuilder` 中没有使用 `private` 和 `final` 的关键字修饰（在 `String` 类当中有 `private` 和 `final` 修复，不过这其实并不是导致 `String` 不可变的原因），同时 `AbstractStringBuilder` 提供了很多修改字符串的方法，比如 `append()`、`insert()`、`delete()`、`replace()` 等。

<!-- more -->

```java
public AbstractStringBuilder append(StringBuffer sb) {
    if (sb == null)
        return appendNull();
    int len = sb.length();
    ensureCapacityInternal(count + len);
    sb.getChars(0, len, value, count);
    count += len;
    return this;
}

// 还有很多其他修改字符串的方法，append() 也有多个重载
```

### 线程安全性

1. `String` 中的对象是不可变的，也就可以理解为每一个 `String` 被创建之后就被作为常量存储，因此是线程安全的
2. `AbstractStringBuilder` 是 `StringBuilder` 和 `StringBuffer` 的公共负累，提供了一些字符串的基本操作，对于 `StringBuilder` 和 `StringBuffer` 来说：
   1. `StringBuilder` 对于这些方法没有加同步锁（synchorized），是非线程安全的
   2. `StringBuffer` 对于这些方法加了同步锁（synchorized），是线程安全的。

![StringBuffer 中的 append() 方法](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220925150456.png)

![StringBuilder 中的 append() 方法](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220925150532.png)


### 性能

1. 对于 `String` 来说，每次对其进行改变实际上是会产生一个新的 `String` 对象，然后将指针指向了新的 `String` 对象
2. `StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用，但关于 `StringBuffer` 需要注意的有两点：
   1. `StringBuffer` 是线程安全的，因此在性能上没有 `StringBuilder` 出色
   2. `StringBuffer` 提供了 `toStringCache` 的一个成员变量用于缓存 `toString()` 方法的字符串，可能会占据更多的空间（但同时也会提升 `toString()` 方法的性能）
3. `StringBuilder` 与 `StringBuffer` 同理，不同点在于其没有保证线程安全，性能较好但在多线程场景下需要考虑线程安全问题；同时由于其没有提供 `toString()` 方法的缓存机制，因此其所占的堆大小可能会比 `StringBuffer` 更少。

> 关于 `StringBuffer` 的 `toStringCache`：
> 
> ```java
> @Override
> public synchronized String toString() {
>   if (toStringCache == null) {
>       toStringCache = Arrays.copyOfRange(value, 0, count);
>   }
>   return new String(toStringCache, true);
> }
> ```
> 
> `toStringCache` 的作用是用作 `toString()` 方法的最后一个缓存，并在 StringBuffer 被修改（modified）时重新变为 null。

#### 对于三者使用的总结

1. 操作少量的数据：适用 `String`
2. 单线程操作字符串缓冲区下操作大量数据：适用 `StringBuilder`
3. 多线程操作字符串缓冲区下操作大量数据：适用 `StringBuffer`


## `String` 为什么是不可变的

`String` 类中使用 `final` 和 `private` 关键字来修饰字符数组保存字符串：

```java
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {

    /** The value is used for character storage. */
    private final char value[];

    // ...
}
```

我们知道被 `final` 关键字修饰的类不能被继承，修饰的方法不能被重写，修饰的变量是基本数据类型则值不能改变，修饰的变量是引用类型则不能再指向其他对象。因此，`final` 关键字修饰的数组保存字符串并不是 `String` 不可变的根本原因，因为这个数组保存的字符串是可变的（`final` 修饰引用类型变量的情况）。

`String` 真正不可变的原因如下：

1. 保存字符串的数组被 `final` 修饰且为私有的，并且 `String` 类没有提供/暴露修改这个字符串的方法。
2. `String` 类被 `final` 修饰导致其不能被继承，进而避免了子类破坏 `String` 不可变。

> 在 Java 9 之后，`String`、`StringBuilder` 与 `StringBuffer` 的实现改用 `byte` 数组存储字符串
> sb
> ```java
> public final class String implements java.io.Serializable,Comparable<String>, CharSequence {
>   // @Stable 注解表示变量最多被修改一次，称为“稳定的”。
>   @Stable
>   private final byte[] value;
>   }
> 
> abstract class AbstractStringBuilder implements Appendable,CharSequence {
>   byte[] value;
> }
> ```
> 
> 新版的 String 其实支持两个编码方案： Latin-1 和 UTF-16。如果字符串中包含的汉字没有超过 Latin-1 可表示范围内的字符，那就会使用 Latin-1 作为编码方案。Latin-1 编码方案下，byte 占一个字节(8 位)，char 占用 2 个字节（16），byte 相较 char 节省一半的内存空间。
> 
> JDK 官方就说了绝大部分字符串对象只包含 Latin-1 可表示的字符。[官方介绍](https://openjdk.java.net/jeps/254)

## 字符串拼接用 "+" 还是用 `StringBuilder`

Java 语言本身并不支持运算符重载，“+”和“+=”是专门为 String 类重载过的运算符，也是 Java 中仅有的两个重载过的运算符。

```java
String str1 = "he";
String str2 = "llo";
String str3 = "world";
String str4 = str1 + str2 + str3;
```

上面的代码对于的字节码如下

![String "+" 字节码](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220925153841.png)

可以看出，字符串对象通过“+”的字符串拼接方式，实际上是通过 `StringBuilder` 调用 `append()` 方法实现的，拼接完成之后调用 `toString()` 得到一个 `String` 对象 。

> 不过，在循环内使用“+”进行字符串的拼接的话，存在比较明显的缺陷：编译器不会创建单个 `StringBuilder` 以复用，会导致创建过多的 `StringBuilder` 对象。

## String equals()

`String` 的 `equals()` 方法是被重写过的，比较的是 `String` 字符串的值是否相等。

## 字符串常量池

// todo

## intern 方法

## Reference

1. [JavaGuide](https://javaguide.cn/java/basis/java-basic-questions-02.html#string)
2. [Java StringBuilder and StringBuffer source code analysis](https://ofstack.com/Java/8234/java-stringbuilder-and-stringbuffer-source-code-analysis.html)
3. [StringBuilder](https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuilder.html)
4. [StringBuffer](https://docs.oracle.com/javase/8/docs/api/java/lang/StringBuffer.html)