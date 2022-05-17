---
title: Cpp Operator Overloading
date: 2022-05-17 20:37:00
description: 南京大学2022春季学期《互联网计算》课程笔记(1)
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku33.jpg
tags:
    - SELearning
    - Cpp
categories:
    - Cpp
---


## 1 概述

C++与Java最不同的地方在我看来可能就是操作符重载了，Java对于自定义类的加减法操作可能需要通过使用自定义的 `add()` 和 `sub()` 方法来实现，而在 C++ 中，可以通过简单的操作符重载来赋予相同的符号以不同的意义（<span style='color: red'>**操作符重载也是一种函数重载**</span>）。这是 C++ 这个语言给予开发者的一个极大的权利与特性，让开发者能够根据自己的设想来实现不同的操作符，但是其也有一定的坏处，是否能够很好地使用这种特性考验的还是开发者的水平。

<!-- more -->

## 2 能够重载的符号

带来了较为方便快捷的操作，在 C++ 中，能够被重载的操作符有以下几种：

| Expression |  As member function  | As non-member function |                           Example                            |
| :--------: | :------------------: | :--------------------: | :----------------------------------------------------------: |
|     @a     |  (a).operator@ ( )   |     operator@ (a)      |           `std::cin` calls `std::cin.operator!()`            |
|    a@b     |  (a).operator@ (b)   |    operator@ (a, b)    |     `std::cout << 42` calls `std::cout.operator << (42)`     |
|    a=b     |  (a).operator= (b)   |  cannot be non-member  | Given `std::string s;, s = "abc";` calls `s.operator=("abc")` |
|  a(b...)   | (a).operator()(b...) |  cannot be non-member  | Given `std::random_device r;, auto n = r();` calls `r.operator()()` |
|    a[b]    |  (a).operator[](b)   |  cannot be non-member  | Given `std::map<int, int> m;, m[1] = 2;` calls `m.operator[](1)` |
|    a->     |  (a).operator-> ( )  |  cannot be non-member  | Given `std::unique_ptr<S> p;, p->bar()` calls `p.operator->()` |
|     a@     |  (a).operator@ (0)   |    operator@ (a, 0)    | Given `std::vector<int>::iterator i;, i++` calls `i.operator++(0)` |

> in this table, `@` is a placeholder representing all matching operators: all prefix operators in @a, all postfix operators other than -> in a@, all infix operators other than = in a@b

需要注意的有以下几点：

1. 如果你重载了 `&&` 或者 `||`，那么这两个运算符会失去<span style='color: red'>**“短路特性”**</span>。

	> 什么是短路特性：比如一个判断语句：`if (1 == 0 && 0 == 0)` 此时 `1 == 0` 不成立的情况下，原生的 `&&` 为了性能会直接跳过下一个条件返回 `false`，这被称为短路特性。

2. 以下几种操作符不能够被重载：`::`、`.`、`.*`、`?:`。

3. 重载运算符并不能够改变运算符的优先级或者操作符的数量

4. 运算符 `->` 的重载必须要么返回一个原始指针，要么返回一个对象（按引用或按值），运算符 `->` 又为其重载。



## 3 例子

我们通过一个例子来看一下 C++ 中典型的操作符重载是如何实现的，通过一个自定义的复数类来作为例子：

```cpp
/**
 * @file operatorOverloading.cpp
 * @author ZY in NJU (201250182@smail.nju.edu.cn)
 * @brief Brief Introduction of Cpp Operator Overloading
 * @version 0.1
 * @date 2022-05-11
 * 
 * @copyright Copyright (c) 2022
 * 
 */
#include <iostream>

class Complex {
private:
    double m_Real {0.0};
    double m_Image {0.0};
public:
    /**
     * @brief Construct a new Complex object
     * 
     * @param real 实部
     * @param image 虚部
     */
    Complex(double real = 0.0, double image = 0.0)
        : m_Real(real), m_Image(image)
    {}

    /**
     * @brief operator = overloading
     * 
     * @param complex anthoer complex
     * @return Complex& complex object reference
     */
    Complex& operator=(const Complex& complex)
    {
        m_Real = complex.m_Real;
        m_Image = complex.m_Image;
        return *this;
    }

    /**
     * @brief prefix ++
     * 
     * @return Complex& complex object reference 
     */
    Complex& operator++()
    {
        m_Real++;
        m_Image++;
        return *this;
    }

    /**
     * @brief postfix ++, int is dummy parameter
     * 
     * @return Complex& comlex object reference
     */
    Complex& operator++(int)
    {
        // 拷贝构造
        Complex temp(*this);
        m_Real++;
        m_Image++;
        // 返回的是拷贝的老对象
        return temp;

        // 返回但是自身++了
    }

    // 友元函数 << >> 的操作符重载
    // 如果直接在类中重载会与原先的操作符写法不一样
    friend std::ostream& operator<<(std::ostream& out, const Complex& complex);
    friend std::istream& operator>>(std::istream& in, Complex& complex);
};

std::ostream& operator<<(std::ostream& out, const Complex& complex)
{
    out << complex.m_Real << "+" << complex.m_Image << "i";
    return out;
}

/**
 * @brief operator >> overloading
 * 
 * @param in in stream, represent cin
 * @param complex comlex object
 * @return std::istream& istream object reference, due to link call
 */
std::istream& operator>>(std::istream& in, Complex& complex)
{
    in >> complex.m_Real >> complex.m_Image;
    return in;
}

/**
 * @brief main function
 * 
 * @return int exit code
 */
int main()
{
    Complex c;
    std::cin >> c;
    std::cout << c << std::endl;
    Complex c1 = c++;
    std::cout << c << std::endl;
    std::cout << c1 << std::endl;
    Complex c2 = ++c;
    std::cout << c << std::endl;
    std::cout << c2 << std::endl;

    return 0;
}
```

需要注意的有几点：

1. 对于 `<<` 和 `>>` 的重载，实际上重载的是 `ostream` 与 `instream` 对该类的操作符，所以需要定义为友元函数，才能够访问该类的成员变量，如果不定义为友元函数进行操作符重载，而是直接在类中进行重载，则写法就会变成 `complext << cin`。

	> 注意函数定义：
	>
	> ```cpp
	> std::ostream& operator<<(std::ostream& out const Complex& complext); // cout不需要更改原有内容，所以设为const变量
	> ```

2. 前缀++与后缀++的区别：我们会发现后缀++有一个没有名字的参数，其实是为了区分前缀++与后缀++的，该参数其实没有任何的作用，被称为“Dummy Parameter”。同时，后缀++在实现的时候经过了一次**拷贝构造**，然后对原有对象进行自增，然后返回拷贝构造的对象，这也是为什么我们认为后缀++是在当前语句执行完后才会对值进行改变的一个原因，不是因为它在语句完之后才调用了后缀++函数，而是因为它返回的是一个++前的拷贝构造出的对象。



## 4 prefix and postfix ++

这里主要是讲一个比较有趣的点，我们会看到很多 C++ 程序员在写循环的时候，喜欢以下的写法：

```cpp
for (int i = 0; i < n ++i)
{
    // do something
}
```

其实这就跟上面讲到的前缀++和后缀++有关，后缀++由于进行了一次拷贝，所以性能上会比前缀++稍微差一点。



## 5 Reference

1. [CppReference](

