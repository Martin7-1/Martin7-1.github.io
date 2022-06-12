---
title: Design Pattern (5)
date: 2022-06-12 21:57:00
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

<!-- more -->

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

## 10 模版方法模式 Template Method Pattern

### 10.1 概述

**定义**：模版方法模式定义了一个操作中的算法的框架，而将一些步骤延迟到子类中。模版方法模式使得子类不改变一个算法的结构即可重定义该算法的某些特定步骤的实现

> 类行为型模式

* 是一种基于继承的代码复用技术
* 将一些复杂流程的实现步骤封装在一系列基本方法中
* 在抽象父类中提供一个称之为**模版方法**的方法来**定义这些基本方法的执行次序**，而通过其子嘞来覆盖某些步骤，从而使得**相同的算法框架**可以有**不同的执行结果**

### 10.2 结构与实现

#### 10.2.1 结构

![template-method-class-diagram](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220612210338.png)

模版方法模式包含以下两个角色：

* `AbstractClass`（抽象类）
* `ConcreteClass`（具体子类）

#### 10.2.2 实现

模版方法模式的实现主要为分两个部分：

1. 模版方法（Template Method）
2. 基本方法（Primitive Method）
   1. 抽象方法（Abstract Method）
   2. 具体方法（Concrete Method）
   3. 钩子方法（Hook Method）

典型的模版方法：

```java
/**
 * 模板方法
 */
public void template() {
    open();
    display();
    if (isPrint()) {
        print();
    }
}

/**
 * 钩子方法
 */
public boolean isPrint() {
    return true;
}
```

典型的抽象类实现：

```java
public abstract class AbstractClass {

    /**
     * 模板方法
     */
    public void templateMethod() {
        primitiveOperation1();
        primitiveOperation2();
        primitiveOperation3();
    }

    /**
     * 基本方法-具体方法
     */
    public void primitiveOperation1() {
        // 这类方法是所有流程相同的
        // 不需要子类重写
    }

    /**
     * 基本方法-抽象方法
     */
    public abstract void primitiveOpeartion2();

    /**
     * 基本方法-钩子方法
     */
    public void primitiveOperation3() {
        // todo
    }
}
```

子类典型代码

```java
public class ConcreteClass extends AbstractClass {

    public void primitiveOpeartion2() {
        // todo
    }

    public void primitiveOpeartion3() {
        // todo
    }
}
```

> 对于客户端来说，其实只需要调用模版方法即可，因此具体流程的方法其实可以实现为 `protected`（子类不需要知道的应该实现为 `private`）。在 `C++` 中，这是一种非常常见的实现一些无法实现为虚函数的“虚化”过程

例：对于友元函数，我们是没办法声明为虚函数的，但是某些友元函数又需要“虚化”的性质以便子类能够在父类基础上进行重写，比如对于 `cout`，我们不希望子类再去写一遍重载 `<<` 的友元函数实现。

```cpp
#include <iostream>

class Vector2 {
protected:
    int x;
    int y;
public:
    Vector2(int x, int y)
        : x(x), y(y)
    {}

    virtual std::ostream& Print(std::ostream& out) const;
    friend std::ostream& operator<<(std::ostream& out, const Vector2& vector2);
};

std::ostream& Vector2::Print(std::ostream& out) const
{
    out << x << ", " << y;
    return out;
}

std::ostream& operator<<(std::ostream& out, const Vector2& vector2) 
{
    return vector2.Print(out);
}

class Vector3 : public Vector2 {
private:
    int z;
public:
    Vector3(int x, int y, int z)
        : Vector2(x, y), z(z)
    {}

    std::ostream& Print(std::ostream& out) const override;
};

std::ostream& Vector3::Print(std::ostream& out) const
{
    out << x << ", " << y << ", " << z;
    return out;
}

int main()
{
    // test
    Vector2 vector2(1, 1);
    std::cout << vector2 << std::endl;
    Vector3 vector3(2, 2, 2);
    std::cout << vector3 << std::endl;

    return 0;
}
```

### 10.3 优缺点与适用环境

#### 10.3.1 优点

* 在父类中形式化地定义一个算法，而由它的子类来实现细节的处理，在子类实现详细的处理算法时并不会改变算法中步骤的执行次序
* 提取了类库中的公共方法，将公共行为放在父类中，而通过其子类来实现不同的行为
* 可实现一种反向控制结构，通过子类覆盖父类的钩子方法来决定某一特定步骤是否需要执行
* 更换和增加新的子类很方便，符合**单一职责原则**和**开闭原则**

#### 10.3.2 缺点

需要为每一个基本方法的不同实现提供一个子类，如果父类中可变的基本方法太多，将会导致类的个数增加，系统会更加庞大，设计也更加抽象（可结合**桥接模式**）

#### 10.3.3 适用环境

* 一次性实现一个算法的不变部分，并将可变的行为留给子类来实现
* 各子类中公共的行为应该被提取出来，并集中到一个公共父类中，以避免代码重复
* 需要通过子类来决定父类算法中某个步骤是否执行，实现子类对父类的反向控制

## 11 访问者模式 Visitor Pattern

### 11.1 概述

* 对象结构中存储了多种不同类型的对象信息
* 对同一对象结构中的元素的操作方式并不唯一，可能需要提供多种不同的处理方式
* 还有可能需要增加新的处理方式

**定义**：访问者模式标识一个作用于某**对象结构**中的各个元素的操作。访问者模式让你可以在不改变各元素的类的前提下定义作用于这些元素的新操作

> 对象行为型模式

* 访问者模式为操作存储不同类型元素的对象结构提供了一种解决方案
* 用户可以对不同类型的元素施加不同的操作

### 11.2 结构与实现

#### 11.2.1 结构

![visitor-pattern-class-diagram](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/20220612213959.png)

访问者模式包含以下5个角色

* `Visitor`（抽象访问者）
* `ConcreteVistor`（具体访问者）
* `Element`（抽象元素）
* `ConcreteElement`（具体元素）
* `ObjectStructure`（对象结构）

#### 11.2.2 实现

典型的抽象访问者类代码

```java
public abstract class Visitor {

    public abstract void visit(ConcreteElementA elementA);

    public abstract void visit(ConcreteElementB elementB);

    public void visit(ConcreteElementC elementC) {
        // 元素elementC操作代码
    }
}
```

典型的具体访问者类代码

```java
public class ConcreteVisitor extends Visitor {

    public void visit(ConcreteElementA elementA) {
        // 元素elementA操作代码
    }

    public void visit(ConcreteElementB elementB) {
        // 元素elementB操作代码
    }
}
```

典型的抽象元素类代码

```java
public interface Element {

    void accept(Vistor visitor);
}
```

典型的具体元素类代码

```java
public class ConcreteElementA implements Element {

    @Override
    public void accept(Visitor visitor) {
        visitor.visit(this);
    }

    public void operationA() {
        // 业务方法
    }
}
```

**双重分派机制**

1. 调用具体元素的 `accept(Visitor visitor)` 方法，并将 `Visitor` 的具体对象作为其参数
2. 在具体元素类的 `accept(Visitor visitor)` 方法内部调用传入的 `Visitor` 所实现的 `visit()` 方法，比如 `visit(ConcreteElementA elementA)` 将当前具体元素的对象(this)作为参数，例如 `visitor.visit(this)`
3. 执行 `Visitor` 对象的 `visit()` 方法，在其中 **还可以调用具体元素对象的业务方法**。

> 两次分派：
> 
> 1. 第一次是具体元素类调用 `accept()` 方法时，根据不同的 `Visitor` 对象分派不同的 `visitor()` 方法。
> 2. 第二次是 `Visitor` 调用 `visit()` 方法时，可以根据不同的具体元素类来调用不同的业务方法

典型对象结构类的实现

```java
import java.util.*;

public class ObjectStructure {

    /**
     * 定义一个集合用于存储元素对象
     */
    private List<Element> list = new ArrayList<>();

    public void accept(Visitor visitor) {
        Iterator<Element> iter = list.iterator();

        while (iter.hasNext()) {
            // 遍历访问集合中的每一个元素
            iter.next().accpet(visitor);
        }
    }

    public void addElement(Element element) {
        list.add(element);
    }

    public void removeElement(Element element) {
        list.remove(element);
    }
}
```

### 11.3 优缺点与适用环境

#### 11.3.1 优点

* 增加新的访问操作很方便
* 将有关元素对象的访问行为集中到一个访问者对下个闹钟功能，而不是分散在一个个的元素类中，类的职责更加清晰
* 让用户能够在不修改现有元素类层次结构的情况下，定义作用于该层次结构的操作

#### 11.3.2 缺点

* 增加新的元素类很困难
* 破坏了对象的封装性

#### 11.3.3 适用环境

* 一个对象结构包含多个类型的对象，希望这些对象实施一些依赖其具体类型的操作
* 需要对一个对象结构中的对象进行很多不同的且不相关的操作，并需要避免让这些操作“污染”这些对象的类，也不希望在增加新操作时修改这些类
* 对象结构中对象对应的类**很少改变**，但经常需要在此对象结构上定义新的操作

## Reference

1. Java设计模式 -- 刘伟，清华大学出版社
2. 面向对象设计方法 -- 南京大学计算机科学与技术系2022春季课程
3. Design Pattern -- GoF 23种设计模式
