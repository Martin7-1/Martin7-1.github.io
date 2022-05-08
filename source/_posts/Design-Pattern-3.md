---
title: Design Pattern (3)
date: 2022-04-28 16:08:03
description: 设计模式中的行为型模式简要介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku8.jpg
tags:
    - SELearning
    - '面向对象'
    - Design Pattern
categories:
    - Design Pattern
---

<span style='color: red'>**行为型模式（Behavioral Pattern）**</span>关注系统中对象之间的交互，研究系统在运行时对象之间的相互通信与协作，进一步明确对象的职责。

行为型模式：不仅仅关注类和对象本身，还重点关注他们之间的**相互作用**和**职责划分**。

* 类行为型模式：使用继承关系在及各类之间分配行为，主要通过多态等方式来分配父类与子类之间的职责
* 对象行为型模式：使用对象的关联关系来分配职责，主要通过对象关联等方式来分配两个或多个类的职责

<!--more-->

![](https://s2.loli.net/2022/04/28/DKNw8TuMtVc2exR.png)



## 1 职责链模式 Chain of Responsibility Pattern

### 1.1 概述

**定义：**避免将一个请求的发送者与接受者耦合在一起，让多个对象都有机会处理请求。将接受请求的对象连接成一条链，并且沿着这条链传递请求，直到有一个对象能够处理它为止。

> 对象行为型模式

* 将请求的处理者组织成一条链，并让请求沿着链传递，由链上的处理者对请求进行相应的处理。
* 客户端无须关心请求的处理细节以及请求的传递，只需将请求发送到链上，将请求的发送者和请求的处理者解耦。



### 1.2 结构与实现

#### 1.2.1 结构

![](https://s2.loli.net/2022/04/28/Llwm79KRMucCk3V.png)

职责链模式包含以下两个角色：

* Handler（抽象处理者）
* ConcreteHandler（具体处理者）

#### 1.2.2 实现

* 典型的具体处理者代码：

```java
public class ConcreteHandler extends Handler {
    public void handleRequest(String request) {
        if (请求满足某种条件) {
            // 处理请求
        } else {
            // 转发请求
            successor.handleRequest(request);
        }
    }
}
```

* 典型的客户端代码

```java
.......
Handler handler1, handler2, handler3;
handler1 = new ConcreteHandlerA();
handler2 = new ConcreteHandlerB();
handler3 = new ConcreteHandlerC();

// 创建职责链
handler1.setSuccessor(handler2);
handler2.setSuccessor(handler3);
// 发送请求，请求对象通常为自定义类型
handler1.handleRequest("请求对象");
......
```

> 个人看法：这里的职责链创建其实我感觉不一定是客户端来完成的，客户只需要知道将请求传递给某个 `handler`，并不需要知道内部职责链的逻辑和传递顺序是如何的，我的看法是在此之上需要添加一个 `Factory` 用来创建职责链。

### 1.3 示例

### 1.4 纯与不纯的职责链模式

1. 纯的职责链模式

	* 一个具体处理者对象只能在两个行为中选择一个：要么承担全部责任，要么将责任推给下家

	* 不允许出现某一个具体处理者对象在承担了一部分或全部责任后又将责任向下传递的情况。

		> 即要么不做直接传递，要么全部做不传递

	* 一个请求必须被某一个处理者对象所接收，不能出现某个请求未被任何一个处理者对象处理的情况。

2. 不纯的职责链模式

	* 允许某个请求被一个具体处理者部分处理后向下传递，或者一个具体处理者处理完某请求后其后继处理者可以继续处理该请求
	* 一个请求可以最终不被任何处理者对象所接收并处理。

> 不纯的职责链模式带来的后果是客户端并不知道自己所传递的请求是否会被正确处理，因此没办法有预想中的结果。另一个问题是，后继处理者的处理可能会覆盖前面处理者的处理，这将导致比如客户端希望获得 `HandlerA` 处理者的处理结果，但是由于 `HandlerA` 的后继处理者 `HandlerB` 的处理结果覆盖了 `HandlerA` 的处理结果，于是客户端就无法收到自己想要的请求处理结果了。

### 1.4 优缺点与适用场景

#### 1.4.1 优点

* 使得一个对象无须知道是其他哪一个对象处理其请求，降低了系统的耦合度
* 可简化对象之间的相互连接
* 给对象职责的分配带来更多的灵活性
* 增加一个新的具体请求处理者时无须修改原有系统的代码，只需要在客户端重新建链即可

#### 1.4.2 缺点

* 不能保证请求一定会被处理
* 对于比较长的职责链，系统性能将受到一定影响，在进行代码调试时不太方便
* 如果建链不当，可能会造成循环调用，将导致系统陷入死循环

#### 1.4.3 适用场景

* 有多个对象可以处理同一个请求，具体哪个对象处理该请求待运行时刻再确定
* 在不明确指定接收者的情况下，向多个对象中的一个提交一个请求
* 可动态指定一组对象处理请求



## 2 命令模式 Command Pattern

> 分离事件处理发送者与请求的最终接受和处理者

### 2.1 概述

**分析**

* 事件处理类 $\leftrightarrow$ 请求的最终接收者和处理者
* 发送者与接收者之间引入了新的**命令对象**（类似电线），将发送者的请求封装在命令对象中，再通过命令对象来调用接收者的方法。
* 作用：相同的按钮可以对应不同的事件处理类

**动机**

* 将请求发送者和接受者完全解耦
* 发送者与接收者之间没有直接引用关系
* 发送请求的对象只需要知道如何发送请求，而不必知道如何发送请求

**定义**：将一个请求封装为一个对象，从而可以用不同的请求对客户进行参数化，对请求排队或者记录请求日志，以及支持可撤销的操作。

> 对象行为型模式

**要点**

* 别名为动作（Action）模式或事务（Transaction）模式
* 用不同的请求对客户进行参数化
* 对请求排队
* 记录请求日志
* 支持可撤销的操作

### 2.2 结构与实现

#### 2.2.1 结构

![](https://s2.loli.net/2022/04/28/aixlTtpLFEDhbMQ.png)

命令模式包含以下4个角色

* Command（抽象命令类）
* ConcreteCommand（具体命令类）
* Invoker（调用者）
* Receiver（接收者）

#### 2.2.2 实现

* 命令模式的本质是对请求进行封装
* 一个请求对应于一个命令，将发出命令的责任和执行命令的责任分开
* 命令模式允许请求的一方和接收的一方独立开来，使得请求的一方不必知道接收请求的一方的接口，更不必知道请求如何被接收、操作是否被执行、何时被执行，以及是怎么被执行的。

* 典型命令类代码

```java
public abstract class Command {
    public abstract void execute();
}
```

* 典型调用者代码

```java
public class Invoker {
    private Command command;
    
    // 构造注入
    public Invoker(Command command) {
        this.command = command;
    }
    
    // setter注入
    public void setCommand(Command command) {
        this.command = command;
    }
    
    // 业务方法
    public void call() {
        command.execute();
    }
}
```

* 具体命令类代码

```java
public class ConcreteCommand extends Command {
    /**
     * 维持一个对请求接收者对象的引用
     */
    private Receiver receiver;
    
    public void execute() {
        // 调用请求接收者的业务处理方法action();
        receiver.action();
    }
}
```

* 典型的请求接收者类代码

```java
public class Receiver {
    public void action() {
        // 具体操作
    }
}
```

### 2.3 实例

### 2.4 命令队列

**动机**

* 当一个请求发送者发送一个请求时，有不止一个请求接收者产生响应，这些请求接收者将逐个执行业务方法，完成对请求的处理。
* 增加一个 `CommandQueue` 类，由该类负责存储多个命令对象，而不同的命令对象可以对应不同的请求接收者
* 批处理

```java
import java.util.*;

public class CommandQueue {
    
    /**
     * 双端队列来存储命令
     */
    private Deque<Command> commandList = new LinkedList<>();
    
    public void addCommand(Command command) {
        commandList.addLast(command);
    }
    
    public void removeCommand(Command command) {
        commandList.remove(command);
    }
    
    public void execute() {
        // 通过for-each获得deque的迭代器
        for (Coomand command : commandList) {
            command.execute();
        }
    }
}
```

对 `Invoker` 我们有如下的改进：

```java
public class Invoker {
    /**
     * 维持一个CommandQueue对象的引用
     */
    private CommandQueue commandQueue;
    
    public Invoker(CommandQueue commandQueue) {
        this.commandQueue = commandQueue;
    }
    
    public void setCommandQueue(CommandQueue commandQueue) {
        this.commandQueue = commandQueue;
    }
    
    public void call() {
        commandQueue.execute();
    }
}
```

### 2.5 记录请求日志

**动机**：将请求的历史记录保存下来，通常以日志文件（Log File）的形式永久存储在计算机中

* 为系统提供一种恢复机制
* 可以用于实现批处理
* 防止因为断电或者系统重启等原因造成请求丢失，而且可以避免重新发送全部请求时造成某些命令的重复执行。

#### 2.5.1 实现

* 将发送请求的命令对象通过序列化写到日志文件中
* 命令类必须实现接口 `Serializable`（实现该类才能够序列化）

### 2.6 实现撤销操作

* 可以通过对命令类进行修改使得系统支持撤销（undo）操作和回复（redo）操作

### 2.7 宏命令

**动机**

* 宏命令（Macro Command）又称为组合命令（Composite Command），它是组合模式和命令模式联用的产物。
* 宏命令是一个具体的命令类，它拥有一个集合，在该集合中包含了对其他命令对象的引用。
* 当调用宏命令的 `execute()` 方法时，将递归调用它所包含的每个成员命令的 `execute()` 方法。一个宏命令的成员可以是简单命令，还可以继续是宏命令。
* 执行一个宏命令将触发多个具体命令的执行，从而实现对命令的批处理。

#### 2.7.1 结构

![](https://s2.loli.net/2022/04/28/UVQbLRoT3PtJSd8.png)

### 2.8 优缺点与适用环境

#### 2.8.1 优点

* 降低系统的耦合度
* 新的命令可以很容易地加入到系统中，符合开闭原则
* 可以比较容易地设计一个命令队列或宏命令（组合命令）
* 为请求的撤销(Undo)和恢复(Redo)操作提供了一种设计和实现方案

#### 2.8.2 缺点

* 使用命令模式可能会导致某些系统有过多的具体命令类（针对每一个对请求接收者的调用操作都需要设计一个具体命令类）

#### 2.8.3 适用环境

* 系统需要将请求调用者和请求接收者解耦，使得调用者和接收者不直接交互
* 系统需要在不同的时间指定请求、将请求排队和执行请求
* 系统需要支持命令的撤销(Undo)操作和恢复(Redo)操作
* 系统需要将一组操作组合在一起形成宏命令



## 3 解释器模式 Interpreter Pattern

> 定义一套文法规则来实现对这些语句的解释，即设计一个自定义的语言。
>
> 基于现有的编程语言 $\rightarrow$ 面向对象编程语言 $\rightarrow$ 解释器模式

### 3.1 概述

**定义**：给定一个语言，定义它的文法的一种表示，并定义一个解释器，这个解释器使用该表示来解释语言中的句子。

> 类行为型模式

* 在解释器模式的定义中所指的“语言”是使用规定格式和语法的代码
* 是一种使用频率相对较低但学习难度相对较大的设计模式，用于描述如何使用面向对象语言构成一个简单的语言解释器。
* 能够加深对面向对象思想的理解，并且理解变成语言中**文法规则**的解释过程。

### 3.2 文法规则和抽象语法树

#### 3.2.1 文法规则

例：$1+2+3-4+1$

```
expression ::= value | operation
operation ::= expression '+' expression | expression '-' expression
value ::= an integer // 一个整数值
```

* `::=` 表示“定义为”
* `|` 表示或
* `{}` 表示组合
* `*` 表示“出现0次或多次”

#### 3.2.2 抽象语法树

> Abstract Syntax Tree, AST

抽象语法树描述了如何构成一个复杂的句子，通过对抽象语法树的分析，可以识别出语言中的终结符类和非终结符类

### 3.3 结构与实现

#### 3.3.1 结构

![](https://s2.loli.net/2022/04/30/qodWCQuzbikPgMy.png)

解释器模式包含以下四个角色：

* `AbstractExpression`（抽象表达式）
* `TerminalExpression`（终结符表达式）
* `NonterminalExpression`（非终结符表达式）
* `Context`（环境类）

#### 3.3.2 实现

* 典型的**抽象表达式类**代码

```java
public abstract class AbstractExpression {
    public abstrat void interpret(Context ctx);
}
```

* 典型的**终结符表达式类**代码

```java
public class TerminalExpression extends AbstractExpression {
    public void interpret(Context ctx) {
        // 终结符表达式的解释操作
    }
}
```

* 典型的**非终结符表达式类**代码

```java
public class NonterminalExpression extends AbstractExpression {
    
    private AbstractExpression left;
    private AbstractExpression Right;
    
    // 构造器注入
    public NonterminalExpression(AbstractExpression left, AbstractExpression right) {
        this.left = left;
        this.right = right;
    }
    
    public void interpret(Context ctx) {
        // 非终结符表达式的解释操作
        // 递归调用每一个组成部分的interpret()方法
        // 在递归调用时指定组成部分的连接方式，即非终结符的功能
    }
}
```

* 环境类 `Context`
	* 用于存储一些全局信息，一般包含一个 `HashMap` 或 `ArrayList` 等类型的集合对象（也可以直接由 `HashMap` 等集合类充当环境类），存储一系列公共信息，例如变量名与值的映射关系（key / value）等，用于在执行具体的解释操作时从中获取相关信息。
	* 可以在环境类中增加一些所有表达式解释器都共有的功能，以减轻解释器的职责。
	* 当系统无需提供全局公共信息时可以省略环境类，根据实际情况决定是否需要环境类。
* 典型的**环境类**代码

```java
public class Context {
    private Map<String, String> map = new HashMap<>();
    
    public void assign(String key, String value) {
        // 往环境类中加值
        map.put(key, value);
    }
    
    public String lookup(String key) {
        // 获取存储在环境类中的值
        return map.get(key);
    }
}
```

### 3.4 示例

### 3.5 优缺点与适用环境

#### 3.5.1 优点

* 易于改变和扩展文法：由于在解释器模式中使用类表示语言的文法规则，因此可以通过继承等机制来改变或扩展文法。
* 可以方便地实现一个简单的语言：每一条文法规则都可以表示为一个类。
* 实现文法较为容易（有自动生成工具）。
* 增加新的解释表达式较为方便，符合开闭原则。

#### 3.5.2 缺点

* 对于复杂文法难以维护：在解释器中每一条规则至少需要定义一个类，因此，一个语言如果包含太多文法规则，类的个数会急剧增加，导致系统难以管理和维护，此时可以考虑语法分析程序等方式来取代解释器模式。
* 执行效率较低：由于在解释器模式中使用了大量的循环和递归调用，因此在解释较为复杂的句子时其速度很慢，而且代码的调用过程也比较麻烦。

#### 3.5.3 适用环境

* 可以将一个需要解释执行的语言中的句子表示为一棵抽象语法树
* 一些重复出现的问题可以用一种简单的语言来进行表达
* 一个语言的文法较为简单
* 执行效率不是关键问题



## 4 迭代器模式 Iterator Pattern

> 访问一个聚合对象中的元素但又不需要暴露它的内部结构。通常包括两个类：聚合类和迭代器类

### 4.1 概述

**分析**

聚合对象的两个职责：

* 存储数据：聚合对象的基本职责。
* 遍历数据：既是可变化的，又是可分离的。

**将遍历数据的行为从聚合对象中分离出来**，封装在迭代器对象中。

由迭代器来提供遍历聚合对象内部数据的行为，简化聚合对象的设计，更符合**单一职责**的原则。

**定义**：迭代器模式提供一种方法**顺序访问**一个聚合对象中各个元素，且不用暴露该对象的内部表示。

> 对象行为型模式

* 又名**游标（Cursor）模式**
* 通过引入迭代器，客户端无须了解聚合对象的内部结构即可实现对聚合对象中成员的遍历，还可以根据需要很方便地增加新的遍历方式。

### 4.2 结构与实现

#### 4.2.1 结构



迭代器模式包含以下4个角色：

* `Iterator` （抽象迭代器）
* `ConcreteIterator` （具体迭代器）
* `Aggregate`（抽象聚合类）
* `ConcreteAggregate`（具体聚合类）

#### 4.2.2 实现

* 典型的抽象迭代器代码

```java
public interface Iterator<T> {
    /**
     * 将游标指向第一个元素
     */
    public void first();
    /**
     * 将游标指向下一个元素
     */
    public void next();
    /**
     * 判断是否存在下一个元素
     */
    public boolean hasNext();
    /**
     * 获取游标指向的当前元素
     */
    public T currentItem();
}
```

* 典型的具体迭代器代码

```java
public class ConcreteIterator<T> implements Iterator<T> {
    /** 
     * 维持一个对具体聚合对象的引用，以便于访问存储在聚合对象中的数据
     */
    private ConcreteAggregate<T> objectList;
    /**
     * 定义一个游标，用于记录当前访问位置
     */
    private int cursor;
    
    public ConcreteIterator(ConcreteAggregate<T> objectList) {
        this.objectList = objectList;
    }
    
    @Override
    public void first() {
        // todo
    }
    
    @Override
    public void next() {
        // todo
    }
    
    @Override
    public boolean hasNext() {
        // todo
    }
    
    @Override
    public T currentItem() {
        // todo
    }
}
```

* 典型的抽象聚合类代码

```java
public interface Aggregate<T> {
    Iterator<T> createIterator();
}
```

* 典型的具体聚合类代码

```java
public class ConcreteAggregate<T> implements Aggregate<T> {
    
    ...
        
    public Iterator<T> createIterator() {
        return new ConcreteIterator(this);
    }
    
    ...
}
```



### 4.3 示例

**结果与分析**

* 如果需要增加一个新的具体聚合类，只需增加一个新的聚合子类和一个新的具体迭代器类即可，原有类库代码无须修改，符合开闭原则
* 如果需要更换一个迭代器，只需要增加一个新的具体迭代器类作为抽象迭代器类的子类，重新实现遍历方法即可，原有迭代器代码无须修改，也符合开闭原则
* 如果要在迭代器中增加新的方法，则需要修改抽象迭代器的源代码，这将违背开闭原则

### 4.4 白箱聚集 vs 黑箱聚集

**宽接口 vs 窄接口**

* 宽接口：一个聚集的接口提供了可以用来修改聚集元素的方法。
* 窄接口：一个聚集的接口没有提供修改聚集元素的方法。

**白箱聚集 vs 黑箱聚集**

白箱聚集：聚集对象为所有对象提供同一个接口（宽接口）

* 迭代子可以从外部控制聚集元素的迭代，控制的仅仅是一个游标（cursor）/ 外禀（Extrinsic）迭代子

> 白箱聚集中，迭代器和聚集类是独立分开的，即可以通过不同的组合形式来组合不同的聚集类和迭代器。

黑箱聚集：狙击对象为迭代子对象提供一个宽接口，而为其他对象提供一个窄接口。同时保证聚集对象的封装和迭代子功能的实现。

* 迭代子是聚集的内部类，可以自由访问聚集的元素。迭代子可以自行实现迭代功能并控制聚集元素的迭代逻辑--内禀迭代子（Intrinsic Iterator）

### 4.5 C++中的迭代器

C++的STL一般都拥有迭代器，这里简要介绍一下 `vector` 的迭代器，相比 Java 的迭代器是通过内部类类来实现对列表的引用持有，C++的迭代器一般是通过返回对应的指针来实现的。比如如下的代码：

```cpp
std::vector<int> list;
std::vector<int>::iterator iter = list.begin();

// list.begin() 返回的迭代器相当于指向vector第一个元素的指针
std::cout << "The first element of the list is " << *iter << std::endl;

// 我们可以通过该迭代器来修改元素
*iter = 5;
// 如果是const iterator的话就无法修改元素
std::vector<int>::const_iterator cIter = list.cbegin();

// 可以通过迭代器来遍历vector
// 需要注意的是 vector.end() 返回的是vector最后元素的下一个位置的指针
for (std::vector<int>::iterator iter = list.begin(); iter != list.end(); iter++)
{
    std::cout << *iter << " ";
}
std::cout << std::endl;

// for-each 是迭代遍历的语法糖
for (auto& element : list) 
{
    std::cout << element << " ";
}
std::cout << std::endl;
```



### 4.6 Java中的迭代器

Java中的 `Iterator` 需要实现的是 `Iterable` 接口，该接口中的唯一方法支持每次调用返回一个新的迭代器。

Java 中的迭代器在 Collection 框架中用于一一检索元素。它是一个通用迭代器，因为我们可以将它应用于任何 Collection 对象。通过使用迭代器，我们可以执行读取和删除操作。它是 Enumeration 的改进版本，具有删除元素的附加功能。 每当我们要枚举所有 Collection 框架实现的接口（如 Set、List、Queue、Deque）和 Map 接口的所有实现类中的元素时，都必须使用迭代器。迭代器是整个集合框架唯一可用的游标。 可以通过调用 Collection 接口中的 iterator() 方法来创建迭代器对象。

```java
Iterator<T> iter = c.iterator();
```

Collection Framework 中的类一般实现 `iterator` 的方式就是通过实现 `iterable` 接口，然后通过内部类实现 `iterator` 接口，然后每次需要迭代器时就通过 `c.iterator()` 返回一个新的内部迭代器（即实现了 `iterator` 接口的内部类）。

Java中的迭代器分为 `Iterator`、`ListIterator`、`Enumeartion` 三种。可以看下面的实例代码：

```java
// Java program to Demonstrate Iterator

// Importing ArrayList and Iterator classes
// from java.util package
import java.util.ArrayList;
import java.util.Iterator;

// Main class
public class Test {
	// Main driver method
	public static void main(String[] args)
	{
		// Creating an ArrayList class object
		// Declaring object of integer type
		ArrayList<Integer> al = new ArrayList<Integer>();

		// Iterating over the List
		for (int i = 0; i < 10; i++)
			al.add(i);

		// Printing the elements in the List
		System.out.println(al);

		// At the beginning itr(cursor) will point to
		// index just before the first element in al
		Iterator<Integer> itr = al.iterator();

		// Checking the next element where
		// condition holds true till there is single element
		// in the List using hasnext() method
		while (itr.hasNext()) {
			// Moving cursor to next element
			int i = itr.next();

			// Getting elements one by one
			System.out.print(i + " ");

			// Removing odd elements
			if (i % 2 != 0)
				itr.remove();
		}

		// Command for next line
		System.out.println();

		// Printing the elements inside the object
		System.out.println(al);
	}
}
```


### 4.7 优缺点与适用环境

#### 4.7.1 优点

* 支持以不同的方式遍历一个聚合对象，在同一个聚合对象上可以定义多种遍历方式
* 简化了聚合类
* 由于引入了抽象层，增加新的聚合类和迭代器类都很方便，无须修改原有代码，符合开闭原则

#### 4.7.2 缺点

* 在增加新的聚合类时需要对应地增加新的迭代器类，类的个数成对增加，这在一定程度上增加了系统的复杂性
* 抽象迭代器的设计难度较大，需要充分考虑到系统将来的扩展。在自定义迭代器时，创建一个考虑全面的抽象迭代器并不是一件很容易的事情

#### 4.7.3 适用环境

* 访问一个聚合对象的内容而无须暴露它的内部表示
* 需要为一个聚合对象提供多种遍历方式
* 为遍历不同的聚合结构提供一个统一的接口，在该接口的实现类中为不同的聚合结构提供不同的遍历方式，而客户端可以一致性地操作该接口