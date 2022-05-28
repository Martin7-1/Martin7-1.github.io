---
title: Design Pattern (4)
date: 2022-05-17 19:08:03
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

<!--more-->

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
     * 创建一个备忘录对象
     */
    public Memento createMemento(Memento m) {
        return new Memento(this);
    }
    
    /**
     * 根据备忘录对象恢复原发器状态
     */
    public void restoreMemento(Memento m) {
        state = m.getState();
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



## 7 观察者模式 Observer Pattern

### 7.1 概述

* 软件系统：一个对象的状态或行为的变化将导致其他对象的状态或行为也发生改变，它们之间将产生联动。
* 观察者模式：
	* 定义了对象之间一种**一对多**的依赖关系，让一个对象的改变能够影响其他对象
	* 发生改变的对象称为观察目标，被通知的对象称为观察者
	* 一个观察目标可以对应多个观察者。

**定义**：观察者模式定义对象之间的一种一对多依赖关系，使得每当一个对象状态发生改变时，其相关依赖对象都得到通知并被自动更新。

> 对象行为型模式

别名：

* 发布-订阅（Public/Subscribe）模式
* 模型-视图（Model/View）模式
* 源-监听器（Source/Listener）模式
* 从属者（Dependents）模式



### 7.2 结构与实现

#### 7.2.1 结构

![image-20220517162134530](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220517162134530.png)

其中，`Subject`的 `nofity` 方法一般实现如下：

```java
for (Observer obs : observerList) {
    obs.update();
}
```

`ConcreteObserver` 的 `update` 方法一般实现如下：

```java
observerState = subject.getState();
```

观察者模式包含以下4个角色：

* `Subject`（目标）
* `ConcreteSubject`（具体目标）
* `Observer`（观察者）
* `ConcreteObserver`（具体观察者）

#### 7.2.2 实现

* 典型的**目标类**实现

```java
public abstract class Subject {
    
    /**
     * 定义一个观察者集合用于存储所有观察者对象
     */
    protected List<Observer> observerList = new ArrayList<>();
    
    /**
     * 注册方法，用于向观察者集合中增加一个观察者
     *
     * @param observer: 要添加的观察者
     */
    public void attach(Observer observer) {
        observers.add(observer);
    }
    
    /**
     * 注销方法，用于在观察者集合中删除一个观察者
     *
     * @param observer: 要移除的观察者
     */
    public void detach(Observer observer) {
        observers.remove(observer);
    }
    
    /**
     * 声明抽象通知方法
     */
    public abstract void notify();
}
```

* 典型的**具体目标类**代码

```java
public class ConcreteSubject extends Subject {
    
    /**
     * 实现通知方法
     */
    public void notify() {
        for (Observer obs : observerList) {
            obs.update();
        }
    }
}
```

* 典型的**抽象观察者**代码

```java
public interface Observer {
    
    /**
     * 声明响应方法
     */
    public void update();
}
```

* 典型的**具体观察者**代码

```java
public class ConcreteObserver implements Observer {
    
    /**
     * 实现响应方法
     */
    public void update() {
        // 具体响应代码
    }
}
```

<span style='color: red'>**说明**</span>：

* 有时候在具体观察者类`ConcreteObserver`中需要使用到具体目标类`ConcreteSubject`中的状态（属性），会存在关联或依赖关系。
* 如果在具体层之间具有关联关系，系统的扩展性将受到一定的影响，<span style='color: red'>增加新的具体目标类有时候需要修改原有观察者的代码</span>，在一定程度上违背了开闭原则，但是如果原有观察者类无须关联新增的具体目标，则系统扩展性不受影响

* 典型的客户端代码片段：

```java
......
Subject subject = new ConcreteSubject();
Observer observer = new ConcreteObserver();

subject.attach(observer); // 注册观察者
subject.notify();
......
```

### 7.3 实例

### 7.4 JDK对观察者模式的支持

`java.util.Observer` 和 `java.util.Observable` 

> 需要注意的是，相比于 Java 传统的以形容词为接口，名词为类不同，这里的 `java.util.Observer` 是一个接口， 代表观察者类，`java.util.Observable` 是一个类，代表目标类

`java.util.Observer` 接口中只有唯一的方法 `update`，用来在目标类更新状态时被调用同步状态。

### 7.5 观察者模式与Java时间处理

* **事件源（Event Source）**：`Subject`
	* 例如：`JButton`、`addActionListener()`：注册方法，`fireXXX()`：通知方法
* **事件监听器（Event Listener）**：`Observer`
	* 例如：`ActionListener`，`actionPerformed()`：响应方法
* **事件处理类（Event Handling Class）**：`ConcreteObserver`
	* 例如：`LoginHandling`：实现 `ActionListener` 接口

### 7.6 观察者模式与MVC

* Model、View、Controller
* 模型可对应于观察者模式中的观察目标，而视图对应于观察者，控制器可充当两者之间的中介者
* 当模型层的数据发生改变时，视图层将自动改变其显示内容。

### 7.7 优缺点与适用环境

#### 7.7.1 优点

* 可以实现表示层和数据逻辑层的分离
* 在观察目标和观察者之间建立一个抽象的耦合
* 支持广播通信，简化了一对多系统设计的难度
* 符合开闭原则，增加新的具体观察者无须修改原有系统代码，在具体观察者与观察目标之间不存在关联关系的情况下，增加新的观察目标也很方便

#### 7.7.2 缺点

* 将所有的观察者都通知到会花费很多时间
* 如果存在**循环依赖**时可能导致系统崩溃
* 没有相应的机制让观察者知道所观察的目标对象是怎么发生变化的，而只是知道观察目标发生了变化

#### 7.7.3 适用环境

* 一个抽象模型有两个方面，其中一个方面依赖于另一个方面，将这两个方面封装在独立的对象中使它们可以各自独立地改变和复用
* 一个对象的改变将导致一个或多个其他对象发生改变，且并不知道具体有多少对象将发生改变，也不知道这些对象是谁
* 需要在系统中创建一个触发链




## 8 状态模式 State Pattern

### 8.1 概述

**状态模式**是为了实现多个状态之间的切换以及不同状态下行为封装问题而衍生出的模式，抛弃了以往复杂且承重的 `if-else` 来判断状态，通过封装状态来实现更加面向对象的程序

**定义**：状态模式是一种允许一个对象在其内部状态改变时改变它的行为的模式。让对象的行为看起来似乎修改了它对应的类。

> 对象行为型模式

* 又名状态对象（Objects for States）
* 用于解决系统中复杂对象的状态转换以及不同状态下行为的封装问题
* 将一个对象的状态从该对象中分离出来，封装到专门的状态类中，使得对象状态可以灵活变化。
* 对于客户端而言，无须关心对象状态的转换以及对象所处的当前状态，无论对于何种状态的对象，客户端都可以一致处理。



### 8.2 结构与实现

#### 8.2.1 结构

![image-20220528201304709](https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/image-20220528201304709.png)

状态模式包含以下3个角色：

* `Context`（环境类）
* `State`（抽象状态类）
* `ConcreteState`（具体状态类）

#### 8.2.2 实现

典型的抽象状态类代码

```java
public abstract class State {
    
    /**
     * 声明抽象业务方法，不同的具体状态类可以有不同的实现
     */
    public abstract void handle();
}
```

典型的具体状态类代码

```java
public class ConcreteState extends State {
    
    @Override
    public void handle() {
        // 方法具体实现代码
    }
}
```

典型的环境类代码

```java
public class Context {
    
    /**
     * 维持一个对抽象状态对象的引用
     */
    private State state;
    /**
     * 其他属性值，可能会导致状态发生变化
     */
    private int value;
    
    public void setState(State state) {
        this.state = state;
    }
    
    public void request() {
        // 其他代码
        // 调用状态对象的业务方法
        state.handle();
        // 其他代码
    }
}
```

状态转换的实现：

```java
public void changeState() {
    if (value == 0) {
        setState(new ConcreteStateA());
    } else if (value == 1) {
        setState(new ConcreteStateB());
    }
}
```

状态转换的实现可以由环境类来负责状态之间的切换，也可以由具体状态类来负责状态之间的转换，可以在具体状态类的业务方法中判断环境类的某些属性来完成状态的切换。



### 8.3 实例

### 8.4 共享状态

* 有些情况下，多个环境对象可能需要共享同一个状态。
* 如果希望在系统中实现多个环境对象共享一个或多个状态对象，那么需要将这些状态对象定义为环境类的<span style='color: red'>**静态**</span>成员对象

### 8.5 优缺点与适用环境

#### 8.5.1 优点

* 封装了状态的转换规则，可以对状态转换代码进行集中管理，而不是分散在一个个业务方法中
* 将所有与某个状态有关的行为放到一个类中，只需要注入一个不同的状态对象即可使环境对象拥有不同的行为
* 允许状态转换逻辑与状态对象合成一体，而不是提供一个巨大的条件语句块，可以避免使用庞大的条件语句来将业务方法和状态转换代码交织在一起
* 可以让多个环境对象共享一个状态对象，从而减少系统中对象的个数

#### 8.5.2 缺点

* 会增加系统中类和对象的个数，导致系统运行开销增大
* 结构与实现都较为复杂，如果使用不当将导致程序结构和代码混乱，增加系统设计的难度
* 对开闭原则的支持并不太好，增加新的状态类需要修改负责状态转换的源代码，否则无法转换到新增状态；而且修改某个状态类的行为也需要修改对应类的源代码

#### 8.5.3 适用环境

* 对象的行为依赖于它的状态（例如某些属性值），状态的改变将导致行为的变化
* 在代码中包含大量与对象状态有关的条件语句，这些条件语句的出现会导致代码的可维护性和灵活性变差，不能方便地增加和删除状态，并且导致客户类与类库之间的耦合增强



## Reference

1. Java设计模式 -- 刘伟，清华大学出版社
2. 面向对象设计方法 -- 南京大学计算机科学与技术系2022春季课程
3. Design Pattern -- GoF 23种设计模式
