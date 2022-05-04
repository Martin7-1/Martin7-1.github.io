---
layout:     post
title:      Cpp Review
subtitle:   Cpp 学习中的一些简单记录？
date:       2022-5-4
author:     ZY
header-img: img/bg13.jpg
catalog: true
tags:
    - SELearning
    - Cpp
---

# Cpp Review

> 记载学习cpp的过程中一些比较容易遗忘和比较特殊的点（相比Java）

## Const

```cpp
const int* p1; // 不能改变指针指向的地址的值
int* const p2; // 不能改变指针指向的地址
```

```cpp
class Entity {
private:
    int m_x, m_y;
public:
    // const代表该函数不会改变任何类的成员变量
    int GetX() const
    {
        return m_x;
    }
}
```



## 初始化成员表

为什么要用初始化成员表：因为如果在构造函数中对成员变量进行赋值，实际上是经历了两步，第一步是成员变量的初始化，第二步才是构造函数中的赋值，这样会导致效率的降低。

初始化成员表的初始化顺序是按照成员变量的定义来的，而不是初始化成员表写的顺序来的

```cpp
class Entity {
private:
    string m_Name;
    int m_age;
public:
    Entity(const string& name, inst age) 
        : m_Name(name), m_age(age)
    {
        
    }
}
```



## Mutable

Mutable是cpp中的一个关键字，其目的在于让某个变量变成可修改的，同时在lambda中也有用处。比如我们将某个类中的方法声明为const:

```cpp
class Entity {
private:
    string m_Name;
public:
    const string GetName() const
    {
        return m_Name;
    }
}
```

将函数声明为const表明在该函数中我们不能对成员变量进行修改，但如果我们有对某个变量进行修改的需求呢？只需要将该变量标上`mutable`即可:

```cpp
class Entity {
private:
    string m_Name;
    mutable int m_Amount;
public:
    const string GetName() const
    {
        m_Amount++;
        return m_Name;
    }
}
```

此时在const函数中，mutable的成员变量是可修改的了



## new and delete

> 什么时候应该使用`new` 和 `delete`?
>
> 在对象所占用的内存很大，或者我们想要在不同的域（Scope）中控制该对象时，我们应该将该对象声明在堆中，否则，我们应该将对象声明在栈中，因为声明在栈中的速度比较快。
>
> 同时应该注意的是，`new` 和 `delete`本质上也是操作符。且相较于`malloc` 和 `free`，`new` 会调用构造函数 而 `delete` 会调用析构函数

```cpp
Entity e;
Entity* e1 = new Entity();
delete e1;

// if Entity have a constructory which is:
// Entity(const string& m_Name);
// we can construct an Entity by this:
Entity e2("Cherno");
Entity* e3 = new Entity("Cherno");
delete e3;
```



## explicit

在C++中, `explicit` 关键字是为了声明某个构造函数是不能被编译器进行隐式的类型转换的，比如我们有如下的代码:

```c++
class Entity {
private
    std::string m_Name;
    int m_Age;
public:
    Entity(const std::string& name) 
        : m_Name(name)
    {}
    
    Entity(int age)
        : m_Age(age)
    {}
};
```

如果我们在`main`函数中写如下的语句，是可以通过编译并且正确执行的。

```cpp
int main() {
    Entity e1 = "Cherno";
    Entity e2 = 22;
}
```

这是因为编译器能够帮助你做隐式的类型转换，只要你有对应的构造函数即可。这种写法等价于下面两种写法。

```cpp
Entity e1("Cherno");
Entity e2(22);

// or
Entity e1 = Entity("Cherno");
Entity e2 = Entity(22);
```

需要注意的是，对编译器来说，隐式转换只会发生一次，例如我们有下面这样的一个函数：

```cpp
void PrintEntity(const Entity& entity)
{
    // Printing
}
```

我们可以看一下下面两种传值方式

```cpp
PrintEntity(22); // right
PrintEntity("Cherno"); // error
```

原因在于下面这一种函数调用实际上传递的是一个`const char[]`，对于编译器来说，这意味着要先把这个数组变成一个`std::string`，再将`std::string`变成`Entity`，此时是无法通过编译的，如果要通过编译，可以通过以下的调用方式：

```cpp
PrintEntity(std::string("Cherno")); // right
```



至于`explict`关键字有什么用呢？就是为了防止这种隐式转换的发生，如果你在构造函数上声明了`explict`，那么编译器就不能够对对应的参数类型进行隐式转换，比如：

```cpp
class Entity {
private
    std::string m_Name;
    int m_Age;
public:
    explicit Entity(const std::string& name) 
        : m_Name(name)
    {}
    
    explicit Entity(int age)
        : m_Age(age)
    {}
};
```

此时，上面的两种有关`Entity`的定义方式就是无效的了。



## Operator Overloading

操作符重载是cpp中的一个十分重要的特性。我们可以通过例子来看一下：

```cpp
struct Vector2 {
    float x, y;
    
    Vector2(float x, float y)
    {
        this->x = x;
        this->y = y;
    }
    
    // normal way and only way in Java
    Vector2 Add(const Vector2& other) const
    {
        return Vector2(x + other.x, y + other.y);
    }
    
    Vector2 operator+(const Vector2& other) const
    {
        return Add(other);
    }
    
    // normal way and only way in Java
    Vector2 SpeedUp(const Vector2& other) const
    {
        return Vector2(x * other.x, y * other.y);
    }
    
    Vector2 operator*(const Vector2& other) const
    {
        return SpeedUp(other);
    }
}
```

在Java中，我们可能只能通过`Add`和`SpeedUp`方法来进行操作，但在`cpp`中，我们可以通过操作符重载来实现我们需要的功能，之后我们调用该操作符和调用对应的方法就是等价的了：

```cpp
Vector2 v3 = v1 + v2; // equal to v1.Add(v2)
Vector2 v4 = v1 * v2; // equal to v1.SpeedUp(v2)
```

在cpp中，操作符还包括`new`、`delete`甚至是`()`等，下面是对`cout`中的`<<`一个重载的例子（类似Java的`toString()`）。对`==`和`!=`的重写则类似于Java的`equals()`

```cpp
std::ostream& operator<<(std::ostream& stream, const Vector2& other)
{
    stream << other.x << ", " << other.y;
}
```



## this

this指针很类似Java中的this。只是在cpp中它是一个指针而不是一个引用。即对`Entity`来说，它代表的类型是`Entity* const`。



## cpp中的对象声明周期

1. 在栈中声明的对象随着当前栈帧的`pop`而被销毁（调用析构函数）
2. 在堆中声明的对象会一直到程序员调用`delete / delete[]` 才会被销毁。

栈帧：任何一个作用域都能成为一个栈帧，比如一个函数，一个类，一个`for`、`while`，甚至简单的一个大括号之间。



## Smart Pointer

> 简单来看，smart pointer是一种会自动帮我们释放内存的指针

### Unique Pointer

声明该指针时 new 一个对象，但是当超过scope的时候该指针会自动delete。该指针是不可被拷贝的。即不能有两个指向同一个内存的`unique pointer`

### Shared Pointer

可以有多个指针指向同一块内存空间，内部有一个对指针的计数器，当计数器置0时（即没有指针指向这块空间的时候），这块空间会自动释放。

它需要新开辟一块空间（`Control Block`）来存储计数。

### Weak Pointer

将SharedPointer拷贝给WeakPointer不会导致计数器的增长，WeakPointer相当于只是保存一个引用但不关心其中是否还指向真正的对象。



## Copying and Copying Constructor

cpp中的拷贝：

栈上声明的对象，赋值操作都是在拷贝，比如：

```cpp
struct Vector2
{
    float x, y;
};

int main()
{
    Vector2 a = { 2, 3 };
    Vector2 b = a; // copying a to b
    b.x = 5; // it will not change the value of a
}
```

但如果是声明在堆中的对象，此时我们持有的是指针，指针之间的赋值会导致两个指针指向同一个对象，即指针中的值拷贝了，但是实际的对象并没有被拷贝。

即：除了引用之间的赋值，其他的赋值其实都是拷贝，会在一个新的内存空间中拷贝一份当前内存空间中的值并存进去。

```cpp
class String
{
private:
    char* m_Buffer;
    int m_Size;
public:
    String(const char* string) 
    {
        m_Szie = strlen(string);
        m_Buffer = new char[m_Size + 1]; // null termination
        memcpy(m_Buffer, string, m_Size);
        m_Buffer[m_Size] = 0; // set the last one to null termination
    }
    
    ~String() 
    {
        delete[] m_Buffer;
    }
    
    // 声明为friend的函数可以访问到该类的private变量
    friend std::ostream& operator<<(std::ostream stream, const String& string);
};

std::ostream& operator<<(std::ostream stream, const String& string) 
{
    stream << string.m_Buffer;
    return stream;
}

int main()
{
    String string = "Cherno";
    String second = string;
    
    std::cout << string << std::endl;
    std::cout << second << std::endl;
    
    // we will get error when we want to destroy the two object
    return 0;
}
```

```cpp
String second = string
```

这里的赋值会使得程序在新的一块内存空间里拷贝一份`string`的成员变量的值。

当我们要调用析构函数释放这两个`String`对象的内存时，我们会发现程序出现了错误，这是因为编译器自动实现的拷贝构造函数实际上是一种“浅拷贝（Shallow Copy）”，即对于某个对象中的指针，它不会将指针指向的值再复制一份，而只是复制一个指针，但是指向的还是同一块内存，当我们调用`delete[]`释放这个指针时，其实它已经被释放过了，因此就会出现错误。

如果我们要实现“深拷贝（Deep Copy）”，我们应该使用拷贝构造函数：

```cpp
String(const String& other)
	: m_Size(other.m_Size)
{
    m_Buffer = new char[m_Size];
    memcpy(m_Buffer, other.m_Buffer, m_Size);
    m_Buffer[m_Size] = 0;
}
```

如果我们不想要拷贝构造函数（即禁止两个对象之间赋值），我们可以这样声明:

```cpp
String(const String& other) = delete;
```

在函数调用中的传参也会拷贝值。因为要将参数压栈，优化性能的做法应该是将参数声明为`const String& string`。



## Arrow Operator

`->`：解引用并调用对应的方法。

```cpp
class Entity {
public:
    void Print() const 
    {
        std::cout << "Hello" << std::endl;
    }
};

int main()
{
    Entity* ptr = new Entity();
    prt->Print();
    
    delete ptr;
    return 0;
}
```

`->`作为操作符，我们也可以重载它。

## Vector

```cpp
#include <iostream>
#include <string>
#include <vector>

struct Vertex
{
    float x, y, z;
    
    Vertex(float x, float y, float z)
        : x(x), y(y), z(z)
    {
            
    }
    
    Vertex(const Vertex& vertex)
        : x(vertex.x), y(vertex.y), z(vertex.z)
    {
        std::cout << "Copied! " << std::endl;
    }
}

std::ostream& operator<<(std::ostream& stream, const Vertex& vertex)
{
    stream << vertex.x << ", " << vertex.y << ", " << vertex.z;
    
    return stream;
}

int main()
{
    std::vector<Vertex> vertices;
    // 向vector里添加元素
    vertices.push_back({ 1, 2, 3 });
    vertices.push_back({ 4, 5, 6 });
    
    for (int i = 0; i < vertices.size(); i++)
    {
        // []的访问是不会检查边界的，可以用at()方法来访问 会检查边界
        std::cout << vertices[i] << std::endl;
    }
    
    // 引用 避免拷贝
    for (Vertex& v : vertices)
    {
        std::cout << v << std::endl;
    }
    
    // 移除vector的第二个元素
    vertices.erase(vertices.begin() + 1);
}
```



### Optimization of Vector

```cpp
#include <iostream>
#include <string>
#include <vector>

struct Vertex
{
    float x, y, z;
    
    Vertex(float x, float y, float z)
        : x(x), y(y), z(z)
    {
            
    }
    
    Vertex(const Vertex& vertex)
        : x(vertex.x), y(vertex.y), z(vertex.z)
    {
        std::cout << "Copied! " << std::endl;
    }
}

std::ostream& operator<<(std::ostream& stream, const Vertex& vertex)
{
    stream << vertex.x << ", " << vertex.y << ", " << vertex.z;
    
    return stream;
}

int main()
{
    std::vector<Vertex> vertices;
    // 向vector里添加元素
    // 以下的三次添加元素会发生六次拷贝
    vertices.push_back(Vertex(1, 2, 3));
    vertices.push_back(Vertex(4, 5, 6));
    vertices.push_back(Vertex(7, 8, 9));
}
```

1. 在main方法的栈帧上创建的`Vertex`需要拷贝到`vector`中 -- 优化：直接在`vector`的地址创建新的`Vertex`。 -- 3次拷贝
2. `vector`初始容量是1，扩容一次变成2，再扩容一次才能容纳三个元素，因此需要拷贝三个`Vertex`（1 + 2） -- 优化：减少Vector的resize

```cpp
vertices.reserve(3); // 给vector3个容量的大小
vertices.emplace_back(Vertex(10, 11, 12)); // 将Vertex直接创建在vector的内存地址的地方，避免拷贝
```



## Using Libraries in Cpp



## 虚函数表

成员函数中自带`const this`指针，比如针对某个类

```cpp
class A
{
public:
    virtual void f();
    void g();
};

class B : public A
{
public:
    void f() {
        g();
    }
    void g();
}
```

在B类的`g()`中，会自带一个`B* const this`的指针，这让动态绑定中存在着静态绑定，比如我们有如下的一个声明：

```cpp
A* p = new B();
p.f();
```

则调`p.f()`时，根据虚函数的规则，会调用实际类型的方法，即B的`f()`，在B类型的`f()`中有`B* const this`指针，所以`f()`中调用的`g()`实际上是`this->g()`。即会调用B的`g()`



虚函数表是在某个内存空间里放了一个所有已经定义的虚函数表，然后再每个定义的类的头部位置之前放一个四个字节大小的内存空间，用来存对应的虚函数指向的虚函数表的内存地址。这样可以保证所有类头部位置存放的字节大小是一样的，也不用担心变长问题，只需要在虚函数表里将所有虚函数的实际地址存放好以便引用即可。

为了实现虚函数，C++ 使用了一种特殊形式的后期绑定，称为虚表。虚拟表是用于以动态/后期绑定方式解析函数调用的函数查找表。虚拟表有时有其他名称，例如“vtable”、“虚拟函数表”、“虚拟方法表”或“调度表”。

虚函数表其实很简单，虽然用文字描述起来有点复杂。首先，每个使用虚函数的类（或派生自使用虚函数的类）都有自己的虚表。该表只是编译器在编译时设置的静态数组。一个虚拟表包含一个条目，对应于类对象可以调用的每个虚函数。此表中的每个条目只是一个函数指针，它指向该类可访问的最衍生函数。 其次，编译器还添加了一个隐藏指针，它是基类的成员，我们将其称为 `*__vptr`。 `*__vptr` 在创建类实例时（自动）设置，以便它指向该类的虚拟表。 *this 指针实际上是编译器用来解析自引用的函数参数，与 `*__vptr` 不同的是，`*__vptr` 是一个真正的指针。因此，它使分配的每个类实例都增大了一个指针的大小。这也意味着 `*__vptr` 被派生类继承，这很重要。 到目前为止，您可能对这些东西如何组合在一起感到困惑，所以让我们看一个简单的例子：

```cpp
class Base
{
public:
    virtual void function1() {};
    virtual void function2() {};
};

class D1: public Base
{
public:
    virtual void function1() {};
};

class D2: public Base
{
public:
    virtual void function2() {};
};
```

因为这里有 3 个类，所以编译器会设置 3 个虚函数表：一个用于 Base，一个用于 D1，一个用于 D2。 编译器还将隐藏指针成员添加到使用虚函数的最顶层基类中。尽管编译器会自动执行此操作，但我们会将其放在下一个示例中，以显示它的添加位置：

```cpp
class Base
{
public:
    VirtualTable* __vptr;
    virtual void function1() {};
    virtual void function2() {};
};

class D1: public Base
{
public:
    virtual void function1() {};
};

class D2: public Base
{
public:
    virtual void function2() {};
};
```

创建类实例时， `*__vptr` 设置为指向该类的虚函数表。例如，当创建 Base 类型的对象时，`*__vptr` 被设置为指向 Base 的虚函数表。当构造 D1 或 D2 类型的对象时，`*__vptr` 被设置为分别指向 D1 或 D2 的虚函数表。 现在，我们来谈谈这些虚拟表是如何填写的。因为这里只有两个虚函数，所以每个虚表将有两个条目（一个用于 function1()，一个用于 function2()）。请记住，当填写这些虚拟表时，每个条目都会填写该类类型的对象可以调用的最派生（即在继承结构里最下层的）函数。 Base 对象的虚拟表很简单。 Base 类型的对象只能访问 Base 的成员。 Base 无法访问 D1 或 D2 功能。因此，function1 的入口指向 Base::function1()，而 function2 的入口指向 Base::function2()。 D1 的虚拟表稍微复杂一些。 D1 类型的对象可以访问 D1 和 Base 的成员。然而，D1 已经覆盖了 function1()，使得 D1::function1() 比 Base::function1() 更加派生。因此，function1 的条目指向 D1::function1()。 D1 没有覆盖 function2()，所以 function2 的入口将指向 Base::function2()。 D2 的虚拟表与 D1 类似，只是 function1 的入口指向 Base::function1()，而 function2 的入口指向 D2::function2()。 这是一张图形化的图片：

![img](https://www.learncpp.com/images/CppTutorial/Section12/VTable.gif)

```cpp
int main()
{
    Base b;
    Base* bPtr = &b;
    bPtr->function1();

    return 0;
}
```

在这种情况下，当 b 创建时，`__vptr` 指向 Base 的虚拟表，而不是 D1 的虚拟表。因此，`bPtr->__vptr` 也将指向 Base 的虚拟表。 Base 的 function1() 的虚拟表条目指向 Base::function1()。因此，bPtr->function1() 解析为 Base::function1()，这是 Base 对象应该能够调用的 function1() 的最衍生版本。 通过使用这些表，编译器和程序能够确保函数调用解析为适当的虚函数，即使您只使用指针或对基类的引用！ 调用虚函数比调用非虚函数慢，原因有二：首先，我们必须使用 `*__vptr` 来访问适当的虚表。其次，我们必须索引虚拟表以找到要调用的正确函数。只有这样我们才能调用该函数。因此，我们必须执行 3 次操作才能找到要调用的函数，而普通间接函数调用需要 2 次操作，直接函数调用需要 1 次操作。然而，对于现代计算机，这个增加的时间通常是微不足道的。 另外提醒一下，任何使用虚函数的类都有一个 `*__vptr`，因此该类的每个对象都会大一个指针。虚函数很强大，但它们确实有性能成本。



## Templates

现在我们需要一个能够打印不同类型的`Print()`函数

```cpp
#include<string>

void Print(int value)
{
    std::cout << value << std::endl;
}

void Print(std::string value)
{
    std::cout << value << std::endl;
}

...
```

我们只改变了传入的参数的类型，但是函数内部的实现其实是基本一样的，这个时候我们可以用模板来实现。实现如下：

```cpp
template<typename T>
void Print(T value)
{
    std:: cout << T << std::endl;
}

int main()
{
    // 不一定需要指定<>，compiler会识别参数的类型并指定
    Print<int>(5);
}
```

模板一开始是不存在，直到我们真正的调用这个函数或这个类，因此，如果我们在某个模板中出现了语法错误，那么编译器可能并不会马上识别出它，而是要到我们调用它的时候才能够发现它的错误。

模板在传不同参数时才会创建对应类型的真正实现，比如对于上面的例子，如果我们没有调用`Print()`函数，那么该函数是不会被编译器创建的（即在内存中是没有这一块代码的内存的）。当我们调用了`Print(5)`时，编译器会为我们创建关于`Print()`函数的`int`版实现，如下所示：

```cpp
void Print(int value)
{
    std::cout << value << std::endl;
}
```

当我们如果继续调用，调用了`Print()`函数的float实现时，比如我们调用了`Print(5.5f)`，那么编译器就会帮我们实现模板函数的float版本，如下所示：

```cpp
void Print(float value)
{
    std::cout << value << std::endl;
}
```

STL: standard template library。标准模板库

```cpp
#include <iostream>
#include <string>

template<typename T, int N>
class Array
{
private:
    T m_Array[N];
public:
    int GetSize() const { return N; }
}

int main()
{
    Array<std::string, 5> array;
    std::cout << array.GetSize() << std::endl;
}
```



## Inheritance in Cpp

**继承的权限访问控制**

cpp中的继承有三种权限访问控制，分别是 `public`、`protected`、`private`。比如如下的语法

```cpp
class Base {
    
};

class Derived : public Base{

};
```

* `public inheritance`：在基类中 `public` 的成员变量和成员方法在派生类中仍然是 `public ` 的，在基类中 `protected` 的成员变量在派生类中仍然是 `protected` 的。
* `protected inheritance`：在基类中 `public ` 和 `protected` 的成员变量和成员方法在派生类中 变成 `protected` 的。
* `private inheritance`：在基类中 `public ` 和 `protected` 的成员变量和成员方法在派生类中变成 `private` 的。

> 需要**注意**的是：基类中 `private` 的成员变量在派生类中是无法被直接访问到的。

```cpp
class Base {
public:
    int x;
protected
    int y;
private:
    int z;
};

class PublicDerived : public Base {
	// x is public
    // y is protected
    // z is not accessible from PublicDerived
};

class ProtectedDerived : protected Base {
	// x is protected
    // y is protected
    // z is not accessible from ProtectedDerived
};

class PrivateDerived : private Base {
	// x is private
    // y is private
    // z is not accessible from PrivateDerived
};
```

### Example 1: Cpp public inheritance

```cpp
#include <iostream>

class Base {
private:
    int m_PrivateMember = 1;
protected:
    int m_ProtectedMember = 2;
public:
    int m_PublicMember = 3;

    // function to access private member from base
    int GetPrivateMember() { return m_PrivateMember; }
};

class PublicDerived : public Base {
public:
    // function to access protected member from Base
    int GetProtectedMember() { return m_ProtectedMember; }
};

int main()
{
    PublicDerived publicDerived;
    std::cout << "Private = " << publicDerived.GetPrivateMember() << std::endl;
    std::cout << "Protected = " << publicDerived.GetProtectedMember() << std::endl;
    std::cout << "Public = " << publicDerived.m_PublicMember << std::endl;

    return 0;
}
```

这里我们用 `public inheritance` 从基类 `Base` 继承到派生类 `PublicDerived`，作为结果，在派生类中：

* `m_ProtectedMember` 继承后**仍然**是 `protected` 的
* `m_PublicMember` 继承后仍然是 `public` 的
* `m_PrivateMember` 继承后是不可被派生类访问的，即仍然是内部可见的 `private` 的。

在 `main()` 方法中，我们是无法访问类的 `protected` 和 `private` 变量的，所以就需要用 `get()` 方法来访问。需要注意的是，`GetPrivateMember()` 方法是定义在基类 `Base` 中的，因为只有在 `Base` 中才访问的到 `private` 的变量，`GetProtectedMember()` 则是定义在派生类 `GetProtectedMember()` 中的

| Accessibility | private members | protected members | public members |
| :------------ | :-------------- | :---------------- | :------------- |
| Base Class    | Yes             | Yes               | Yes            |
| Derived Class | No              | Yes               | Yes            |



### Example 2: Cpp protected Inheritance

```cpp
#include <iostream>

class Base {
private:
    int m_PrivateMember = 1;
protected:
    int m_ProtectedMember = 2;
public:
    int m_PublicMember = 3;

    // function to access private member from base
    int GetPrivateMember() { return m_PrivateMember; }
    int GetProtectMember() { return m_ProtectedMember; }
};

class ProtectedDerived : protected Base {
public:
    int GetProtectedMember() { return m_ProtectedMember; }
    int GetPublicMember() { return m_PublicMember; }
};

int main()
{
    ProtectedDerived protectedDerived;
    // protectedDerived.GetPrivateMember() 作为protected的成员方法被继承，无法被外部访问
    std::cout << "Private can not accessed, because the function GetPrivateMember() is inherited as protected" << std::endl;
    std::cout << "Protected = " << protectedDerived.GetProtectedMember() << std::endl;
    std::cout << "Public = " << protectedDerived.GetPublicMember() << std::endl;
    
    return 0;
}
```

在这里，我们在 `protected inheritance` 下从 `Base` 派生了 `ProtectedDerived`。 因此，在 `ProtectedDerived` 中： `m_ProtectedMember`、`m_PublicMember` 和 `GetPrivateMember()` 被继承为 `protected` 的。 `m_PrivateMember` 不可访问，因为它在 `Base` 中是私有的。 众所周知，`protected` 的成员变量和成员方法不能从类外部直接访问。因此，我们不能使用 `ProtectedDerived` 中的 `GetPrivateMember()` 方法。 这也是我们需要在 `ProtectedDerived` 中创建 `GetPublicMember()` 方法以访问 `m_PublicMember` 变量的原因。

| Accessibility | private members | protected members | public members                         |
| :------------ | :-------------- | :---------------- | :------------------------------------- |
| Base Class    | Yes             | Yes               | Yes                                    |
| Derived Class | No              | Yes               | Yes (inherited as protected variables) |

### Example 3: Cpp private Inheritance

```cpp
#include <iostream>

class Base {
private:
    int m_PrivateMember = 1;
protected:
    int m_ProtectedMember = 2;
public:
    int m_PublicMember = 3;

    // function to access private member from base
    int GetPrivateMember() { return m_PrivateMember; }
    int GetProtectMember() { return m_ProtectedMember; }
};

class PrivateDerived : private Base {
public:
    int GetProtectedMember() { return m_ProtectedMember; }
    int GetPublicMember() { return m_PublicMember; }
};

int main()
{
    PrivateDerived privateDerived;
    std::cout << "Private can not accessed, because the function GetPrivateMember() is inherited as private" << std::endl;
    std::cout << "Protected: " << privateDerived.GetProtectedMember() << std::endl;
    std::cout << "Public: " << privateDerived.GetPublicMember() << std::endl;

    return 0;
}
```

在这里，我们以 `private inheritance` 从 `Base` 派生出 `PrivateDerived`。 因此，在 `PrivateDerived` 中： `m_ProtectedMember`、`m_PublicMember` 和 `GetPrivateMember()` 作为私有的成员变量和成员方法继承。 `m_PrivateMember` 不可访问，因为它在 `Base` 中是私有的。 众所周知，私有成员不能从类外部直接访问。因此，我们不能使用 `PrivateDerived` 的 `GetPrivateMember`。 这也是我们需要在 `PrivateDerived` 中创建 `GetPublicMember()` 函数以访问 `m_PublicMember` 变量的原因。

| Accessibility | private members | protected members                    | public members                       |
| :------------ | :-------------- | :----------------------------------- | :----------------------------------- |
| Base Class    | Yes             | Yes                                  | Yes                                  |
| Derived Class | No              | Yes (inherited as private variables) | Yes (inherited as private variables) |



