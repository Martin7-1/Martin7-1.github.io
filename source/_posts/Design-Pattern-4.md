---
title: Design Pattern (4)
date: 2022-05-12 16:08:03
description: 设计模式中的行为型模式简要介绍
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku32.jpg
tags:
    - SELearning
    - '面向对象'
    - Design Pattern
categories:
    - Design Pattern
---

## 5 中介者模式 Mediator Pattern

### 5.1 概述

网状结构的软件系统：多对多联系将导致系统非常复杂，1几乎每个对象都需要与其他对象发生相互作用，而这种相互作用表现为一个对象与另一个对象的直接耦合，这将导致一个过度耦合的系统。

![image-20220512141841852](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220512141841852.png)

**如何解决这种过度耦合的结构？**

将其转换为**<span style='color: red'>星型结构</span>**：中介者模式<span style='color: red'>将系统的网状结构变成以中介者为中心的星型结构</span>，同事对象不再直接与另一个对象联系，它通过中介者对象与另一个对象发生相互作用。系统的结构不会因为新对象的引入带来大量的修改工作。



**定义**：**<span style='color: red'>中介者模式</span>**通过定义一个对象来封装一系列对象的交互。中介者模式使各个对象之间不需要显式的相互引用，从而使其耦合松散，而且让你可以独立地改变它们之间的交互。

> 对象行为型模式

* 又称为调停者模式
* 在中介者模式中，通过引入中介者来简化对象之间的复杂交互。
* 中介者模式是迪米特法则的一个典型应用
* 对象之间多对多的复杂关系转化为相对简单的一对多关系



### 5.2 结构与实现

#### 5.2.1 结构

![image-20220512142919284](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220512142919284.png)

中介者模式包含以下4个角色：

* `Mediator`（抽象中介者）
* `ConcreteMediator`（具体中介者）
* `Colleague`（抽象同事类）
* `ConcreteColleague`（具体同事类）



#### 5.2.2 实现

**中介者类的职责**：

* **中转作用（结构性）**：各个同事对象不再需要显式地引用其他同事，当需要和其他同事进行通信时，可通过中介者来实现间接调用。
* **协调作用（行为性）**：中介者可以更进一步的对同事之间的关系进行封装，同事可以一致地和中介者进行交互，而不需要指明中介者需要具体怎么做，中介者根据封装在自身内部的协调逻辑对同事的请求进行进一步处理，将同事成员之间的关系行为进行分离和封装。

典型的**抽象中介者类**代码

```java
public abstract class Mediator {
    
    /**
     * 用于存储同事对象的列表
     */
    protected List<Colleague> colleagueList = new ArrayList<>();
    
    /**
     * 用于增加同事对象
     */
    public void register(Colleague colleague) {
        colleagueList.add(colleague);
    }
    
    /**
     * 声明抽象的业务方法
     */
    public abstract void operation();
}
```

典型的**具体中介者类**代码

```java
public class ConcreteMediator extends Mediator {
    
    /**
     * 实现业务方法，封装同事之间的调用
     */
    @Override
    public void operation() {
        ConcreteColleagueA concreteColleagueA = (ConcreteColleagueA) colleagueList.get(0);
        
        // 通过中介者调用同事类的方法
        // 同事可能有非抽象的方法 所以需要强制转换。
        concreteColleagueA.method1();
        concreteColleagueA.specialMethod();
    }
}
```

典型的**抽象同事类**代码

```java
public abstract class Colleague {
    
    /**
     * 维持一个抽象中介者的引用
     */
    protected Mediator mediator;
    
    public Colleague(Mediator mediator) {
        this.mediator = mediator;
    }
    
    public abstract void method1();
    
    public void method2() {
        mediator.operation();
    }
}
```

典型的**具体同事类**代码：

```java
public class ConcreteColleague extends Colleague {
    
    public ConcreteColleague(Mediator mediator) {
        super(mediator);
    }
    
    @Override
    public void method1() {
        .....
    }
    
    public void specialMethod();
}
```



### 5.3 示例

### 5.4 扩展中介者与同事类

如果需要增加同事类，好的解决办法是增加 `ConcreteMediator` 的子类，这样比较符合开闭原则。

> 注：个人看法，每增加一个同事类别就要实现一个中介者的子类，这样频繁的实现子类可能会造成**继承结构过深**（即继承的层级过多），这样的话父类需要承担较大的稳定性和职责（即父类必须要保证不能有变动，因为父类的变动会导致其下的子类全部都需要变动）。我的看法是如果需要增加同事类，一个方式是如上面实现中维护一个同事类的 `List`，如果增加新的具体同事类，只可以通过再实现一个 `ConcreteMediator` 然后通过持有旧的引用来进行更改。
>
> 或者我觉得还是需要更加抽象化同事类的功能，才能够更加解耦。



### 5.5 优缺点适用环境

#### 5.5.1 优点

* 简化了对象之间的交互，它用中介者和同事的一对多交互代替了原来同事之间的多对多交互，将原本难以理解的网状结构转换成相对简单的**星型结构**
* 可将各同事对象解耦
* 可以减少子类生成，中介者模式将原本分布于多个对象间的行为集中在一起，改变这些行为只需生成新的中介者子类即可，这使得各个同事类可被重用，无须直接对同事类进行扩展

#### 5.5.2 缺点

* 在具体中介者类中包含了大量的同事之间的交互细节，可能会导致具体中介者类非常复杂，使得系统难以维护

> 中介者的职责十分重，如果出现了需求的变换或者增加，可能会导致中介者类需要大幅度的修改。

#### 5.5.3 适用环境

* 系统中对象之间存在复杂的引用关系，系统结构混乱且难以理解
* 一个对象由于引用了其他很多对象并且直接和这些对象通信，导致难以复用该对象
* 想通过一个中间类来封装多个类中的行为，又不想生成太多的子类



## 6 备忘录模式 Memento Pattern

> 软件系统中的后悔药 -- 撤销（Undo）

### 6.1 概述

* 通过使用备忘录模式可以让系统恢复到某一特定的历史状态
* 首先**<span style='color: red'>保存软件系统的历史状态</span>**，当用户需要取消错误操作并且返回某个历史状态时，可以**<span style='color: red'>取出事先保存的历史状态</span>**来覆盖当前状态。

**定义**：在不破坏封装的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态，这样就可以在以后将对象恢复到原先保存的状态。

> 对象行为型模式

* 别名为标记（Token）模式
* 提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤
* 当前在很多软件所提供地撤销（Undo）操作中就使用了备忘录模式



### 6.2 结构与实现

#### 6.2.1 结构

![image-20220512150048095](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220512150048095.png)

备忘录模式包含以下3个角色：

* `Originator`（原发器）
* `Memento`（备忘录）
* `Caretaker`（负责人）



#### 6.2.2 实现

典型的**原发器**代码

```java
package designpatterns.memnto;

public class Originator {
    
    /**
     * 状态，根据业务需求来维持对应类型的引用
     */
    private String state;
    
    /**
     * 根据备忘录对象恢复原发器状态
     */
    public Memento createMemento(Memento m) {
        state = m.state;
    }
    
    public void setState(String state) {
        this.state = state;
    }
    
    public String getState() {
        return this.state;
    }
}
```

典型的**备忘录**代码

```java
/**
 * 备忘录类，默认可见性包内可见，不允许除原发器的其他类修改备忘录的状态
 */
class Memento {
    
    private String state;
    
    Memento(Originator o) {
        state = o.getState();
    }
    
    void setState(String state) {
        this.state = state;
    }
    
    String getState() {
        return this.state;
    }
}
```

**<span style='color: red'>注意</span>**：

* 除了 `Originator` 类，不允许其他类来调用备忘录类 `Memento` 的构造函数与相关的方法
* 如果允许其他类调用 `setState()` 等方法，将导致在备忘录中保存的历史状态发生改变，通过撤销操作所恢复的状态就不再是真实的历史状态，备忘录模式也就失去了本身的意义
* 理想的情况是只允许生成该备忘录的原发器访问备忘录的内部状态

> Java语言实现：
>
> 1. 将 `Memento` 类与 `Originator` 类定义在同一个包中来实现封装，因为 Java 的默认可以可见性是包内可见，这样就避免了其他类来访问 `Memento` 类，即保证其在包内可见。
> 2. 将备忘录类作为原发器类的内部类，使得只有原发器才可以访问备忘录中的数据，其他对象都无法使用备忘录中的数据。



典型的**负责人类**代码

```java
package designpatterns.memento;

public class CareTaker {
    
    private Memento memento;
    
    public Memento getMemento() {
        return memento;
    }
    
    public void setMemento(Memento memento) {
        this.memento = memento;
    }
}
```



### 6.3 示例

### 6.4 实现多次撤销

有时候用户需要撤销多步操作

**实现方案**：在负责人类中定义一个集合来存储多个备忘录，每备忘录负责保存一个历史状态，在撤销时可以对备忘录集合进行逆向遍历，回到一个指定的历史状态，还可以对备忘录集合进行正向遍历，实现重做（Redo）或恢复操作，即取消撤销，让对象状态得到恢复

```java
public class MementoCareTaker {
    
    private List<Memento> mementoList = new ArrayList<>();
    
    public Memento getMemento(int index) {
        return memenList.get(index);
    }
    
    public void setMemento(Memento memento) {
        mementoList.add(memento);
    }
}
```



### 6.5 优缺点与适用环境

#### 6.5.1 优点

* 提供了一种状态恢复的实现机制，使得用户可以方便地回到一个特定的历史步骤
* 实现了对信息的封装，一个备忘录对象是一种原发器对象状态的表示，不会被其他代码所改动

#### 6.5.2 缺点

* 资源消耗过大，如果需要保存的原发器类的成员变量太多，就不可避免地需要占用大量的存储空间，每保存一次对象的状态都需要消耗一定的系统资源

#### 6.5.3 适用环境

* 保存一个对象在某一个时刻的全部状态或部分状态，这样以后需要时能够恢复到先前的状态，实现撤销操作
* 防止外界对象破坏一个对象历史状态的封装性，避免将对象历史状态的实现细节暴露给外界对象



## Reference

1. Java设计模式 -- 刘伟，清华大学出版社
2. 面向对象设计方法 -- 南京大学计算机科学与技术系2022春季课程
3. Design Pattern -- GoF 23种设计模式
