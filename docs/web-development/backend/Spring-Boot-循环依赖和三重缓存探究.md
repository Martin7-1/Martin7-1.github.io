---
title: Spring 循环依赖
date: 2023-02-27 16:07:20
description: Spring 中循环依赖问题的探究
top_img: https://img-bed-1309306776.cos.ap-shanghai.myqcloud.com/img/miku92.jpeg
tags:
   - Java
   - Spring
categories:
   - Java
   - Spring
---

## 什么是循环依赖

在 Spring 中，循环依赖有如下的几种形式（箭头代表依赖关系）

1. BeanA -> BeanB -> BeanA（依赖形成闭环关系）
2. BeanA -> BeanA（自己依赖自己）

<!-- more -->

在正常的 Bean 初始化和实例化过程中，假设我们当前有如下依赖关系：`BeanA -> BeanB -> BeanC`，Spring 会创建 `BeanC`；然后创建 `BeanB`（在创建 `BeanB` 的过程中将 `BeanC` 注入到 `BeanB` 中）；然后创建 `BeanA`（在创建 `BeanA` 的过程中将 `BeanB` 注入到 `BeanA` 中）。但是在**循环依赖**中，由于它们的互相依赖关系，Spring 不知道应该先初始化哪一个 Bean。在这种情况下，Spring 会在加载 application context 的时候抛出 `BeanCurrentlyInCreationException`

> 需要注意的是，上述的这种循环依赖情况出现在**构造器注入**的情况下（针对单例 Bean，如果是 prototype 的 Bean 类型，则 Field 注入也有可能出现循环依赖），如果我们使用的是 field 注入或者 setter 注入，那么是不会出现这种情况的，因为在后两者情况下，依赖注入会发生在需要这些 Bean 的时候（而不是加载 context 的时候）

### 一个循环依赖的例子

```java
@Component
public class CircularDependencyA {

    private CircularDependencyB circB;

    @Autowired
    public CircularDependencyA(CircularDependecyB circB) {
        this.circB = circB;
    }
}
```

```java
@Component
public class CircularDependencyB {

    private CircularDependencyA circA;

    @Autowired
    public CircularDependencyB(CircularDependecyA circA) {
        this.circA = circA;
    }
}
```

当我们使用 `JUnit5` 进行测试的时候（比如使用 contextLoaded 进行测试，不需要任何的测试代码，因为这是发生在 context 加载阶段，即 Spring 自己的内部代码初始化阶段），就会抛出类似如下的 message：

```bash
BeanCurrentlyInCreationException: Error creating bean with name 'circularDependencyA':
Requested bean is currently in creation: Is there an unresolvable circular reference?
```

## 解决方法初窥探

### 重新设计

当出现循环依赖的时候，其实说明代码的设计是不好的，因为两个类的互相依赖代表这两个类的职责是没有做到很好的分离的（没有依赖于高层 / 抽象对象，没有做到职责的较好拆分和设计）。但是在实际项目当中，出于各种因素（比如项目时间因素、已有代码无法变动因素），很有可能会出现循环依赖的情况，当我们无法对 Bean 之间的依赖关系进行重新设计的时候，我们可以尝试其他的办法。

> 良好的面相对象设计不应该出现循环依赖，这说明职责没有很好分离；且好的设计应该是依赖于接口而不是依赖于具体类的；

### 使用 @Lazy

最简单的方法之一是使用 `@Lazy` 注解来告诉 Spring 懒加载一个 Bean（即稍晚进行依赖注入，被标注了 `@Lazy` 注解的 Bean 会在第一次需要的时候进行依赖注入），在更改之后，上述的例子如下所示：

```java
@Component
public class CircularDependencyA {

    private CircularDependencyB circB;

    @Autowired
    public CircularDependencyA(@Lazy CircularDependecyB circB) {
        this.circB = circB;
    }
}
```

关于 `@Lazy`，使用和注意点如下：

1. `@Lazy` 被使用在标注了 `@Component`（或者有相同语义的注解，比如 `@Service` 等）的类或者标注了 `@Bean` 的方法上
   1. 如果被设置为 `true`，那么该 Bean 不会初始化，直到被其他 Bean 依赖或者使用 `BeanFactory` 显式的去进行查找
   2. 如果被设置为 `false`，那么就会和其他普通 Bean 一样，饥饿的（eager）去进行单例的初始化
2. `@Lazy` 被使用在 `@Configuration` 标注的类上，这说明在这个 Configuration 中的所有 Bean（即用 `@Bean` 标注的方法对应的 Bean）都需要进行懒加载。注意：如果 method 上也有 `@Lazy` 注解且设置为 false，那么就会覆盖掉 class 上的 `@Lazy` 注解
3. `@Lazy` 被使用在依赖注入的地方（`@Autowired` 或者 `@Inject`），需要注意的是，在这种情况下，`@Lazy` 会为所有受影响的依赖 Bean 创建延迟解析的代理（proxy），作为使用 `ObjectFactory` 或者 `Provieder` 的替代方法。在这种情况下，如果依赖 Bean 不存在，则只能通过调用时的异常来查找。因此，这样的依赖注入会导致可选依赖项（Optional Dependency）在依赖注入时的异常。对于允许更复杂的惰性引用的编程等效项，应该要考虑使用 `ObjectProvider`。

### 使用 Setter / Field 注入

这是 [Spring 官方文档推荐](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans)的方式，更改也非常简单：

```java
@Component
public class CircularDependencyA {

    private CircularDependencyB circB;

    @Autowired
    public void setCircB(CircularDependencyB circB) {
        this.circB = circB;
    }

    public CircularDependencyB getCircB() {
        return circB;
    }
}
```

### 使用 @PostConstruct

```java
@Component
public class CircularDependencyA {

    @Autowired
    private CircularDependencyB circB;

    @PostConstruct
    public void init() {
        circB.setCircA(this);
    }

    public CircularDependencyB getCircB() {
        return circB;
    }
}
```

`@PostConstruct` 的作用：Spring calls the methods annotated with `@PostConstruct` only once, just after the initialization of bean properties.

### 实现 `ApplicationContextAware` 和 `InitializingBean` 接口

这种方法实际上就是从 context 中去手动获取 Bean 然后进行对应的 field 设置。

```java
@Component
public class CircularDependencyA implements ApplicationContextAware, InitializingBean {

    private CircularDependencyB circB;

    private ApplicationContext context;

    public CircularDependencyB getCircB() {
        return circB;
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        circB = context.getBean(CircularDependencyB.class);
    }

    @Override
    public void setApplicationContext(final ApplicationContext ctx) throws BeansException {
        context = ctx;
    }
}
```

## Bean 的单例循环依赖解决机制解读

> 需要注意的是下面的机制是针对 setter or field 注入的循环依赖解决机制解读，对于构造器注入的循环依赖问题，Spring 本身无法直接解决，需要通过上面的方式来进行解决

首先我们需要理解为什么是在构造器注入的时候会出现循环依赖的问题，但是在 setter 注入 or field 注入的时候不会出现这个问题，这是因为 Java 的引用传递在获取的时候，对应引用的 field 是可以延后设置的，但是对于构造器来说，要获取一个对象的引用，这个对象必须要执行过构造方法（即对象必须初始化完成）

Spring 产生一个 Bean 需要以下的几个步骤：

1. createBeanInstance：实例化一个 Bean，实际上就是调用构造方法实例化对象
2. populateBean：填充属性，对 Bean 的依赖属性进行注入
3. initializeBean：初始化一个 Bean（注意初始化和实例化是有区别的），调用 `initializeBean` 方法进行初始化

Spring 中的核心代码如下：

```java
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) throws BeanCreationException {
    // Instantiate the bean.
    BeanWrapper instanceWrapper = null;
    if (mbd.isSingleton()) {
        instanceWrapper = this.factoryBeanInstanceCache.remove(beanName);
    }
    if (instanceWrapper == null) {
        instanceWrapper = createBeanInstance(beanName, mbd, args);
    }
    Object bean = instanceWrapper.getWrappedInstance();
    Class<?> beanType = instanceWrapper.getWrappedClass();
    if (beanType != NullBean.class) {
        mbd.resolvedTargetType = beanType;
    }

    // ...，这里省略部分代码

    // Eagerly cache singletons to be able to resolve circular references
    // even when triggered by lifecycle interfaces like BeanFactoryAware.
    boolean earlySingletonExposure = (mbd.isSingleton() && this.allowCircularReferences && isSingletonCurrentlyInCreation(beanName));
    if (earlySingletonExposure) {
        if (logger.isTraceEnabled()) {
            logger.trace("Eagerly caching bean '" + beanName + "' to allow for resolving potential circular references");
        }
        // 这里实际上就是添加到三级缓存当中
        addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));
    }

    // Initialize the bean instance.
    Object exposedObject = bean;
    try {
        // 对 Bean 进行依赖注入（属性填充）
        populateBean(beanName, mbd, instanceWrapper);
        exposedObject = initializeBean(beanName, exposedObject, mbd);
    } catch (Throwable ex) {
        if (ex instanceof BeanCreationException bce && beanName.equals(bce.getBeanName())) {
            throw bce;
        } else {
            throw new BeanCreationException(mbd.getResourceDescription(), beanName, ex.getMessage(), ex);
        }
    }

    // ... 省略部分代码

    return exposedObject;
}
```

Spring 中为了解决循环依赖的问题，使用了**三级缓存**

1. `singletonObjects`：单例对象的 cache，类型是 `ConcurrentHashMap`
2. `singletonFactories`：单例对象工厂的 cache，类型是 `HashMap`
3. `earlySingletonObjects`：饥饿加载的单例对象的 cache，类型是 `HashMap`

Spring 获取 Bean 的步骤如下（省略相关源码）：

1. Spring 首先从一级缓存 `singletonObjects` 中获取。
2. 如果获取不到，并且对象正在创建中，就再从二级缓存 `earlySingletonObjects` 中获取。
3. 如果还是获取不到且允许 `singletonFactories` 通过 `getObject()` 获取，就从三级缓存 `singletonFactory.getObject()` (三级缓存)获取.
4. 如果从三级缓存中获取到就从 `singletonFactories` 中移除，并放入`earlySingletonObjects` 中。其实也就是从三级缓存移动到了二级缓存。

```java
protected Object getSingleton(String beanName, boolean allowEarlyReference) {
    // Quick check for existing instance without full singleton lock
    Object singletonObject = this.singletonObjects.get(beanName);
    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
        singletonObject = this.earlySingletonObjects.get(beanName);
        if (singletonObject == null && allowEarlyReference) {
            synchronized (this.singletonObjects) {
                // Consistent creation of early reference within full singleton lock
                singletonObject = this.singletonObjects.get(beanName);
                if (singletonObject == null) {
                    singletonObject = this.earlySingletonObjects.get(beanName);
                    if (singletonObject == null) {
                        ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                        if (singletonFactory != null) {
                            singletonObject = singletonFactory.getObject();
                            this.earlySingletonObjects.put(beanName, singletonObject);
                            this.singletonFactories.remove(beanName);
                        }
                    }
                }
            }
        }
    }

    return singletonObject;
}
```

核心其实就是 `singletonFactories` 这个三级 cache，其实就是通过在创造的时候提前将对象工厂（其实也就是对应的 Bean 的代表）加入到三级 cache 中，在其他对象进行初始化的时候，如果在一二级 cache 中没有找到对应的 Bean，就能够在三级 cache 中找到对应的还未初始化完全的 Bean。

举个例子：

1. BeanA 完成了初始化的第一步（此时还没有 `populateBean()`），将自己加入到了 `singletonFactories` 中。
2. 然后进行 `populateBean()` 发现自己依赖于 BeanB，于是就尝试去获取 BeanB。
3. 但是由于 BeanB 还没有初始化，所以会调用 `doCreateBean()` 来创建 BeanB。
4. BeanB 在初始化过程中发现自己依赖了 BeanA，于是就尝试去获取 A。
5. 在一级 cache 和二级 cache 中都没有获取到对应的 Bean，但是由于之前提到 BeanA 在实例化完将自己加入到了 `singletonFactories` 中，于是此时 BeanB 可以在三级缓存中找到 BeanA，从而完成 BeanB 的初始化。
6. BeanB 完成初始化后，BeanA 拿到了 BeanB 对象，从而也顺利的完成了初始化

## 什么样的循环依赖是 Spring 无法处理的

> 这里指的是 Spring 在没有其他特殊处理下，无法解决的循环依赖情况

1. 构造器注入的循环依赖情况（请通过使用前面说明的方式来进行解决）
2. Spring 不支持 prototype Bean field 注入循环依赖，对于这类 Bean，Spring 只有在需要的时候才会初始化这个 Bean

## Reference

1. [Circular Dependency in Spring](https://www.baeldung.com/circular-dependencies-in-spring)
2. [DefaultSingletonBeanRegistry doc](https://docs.spring.io/spring-framework/docs/3.2.0.M2_to_3.2.0.RC1/Spring%20Framework%203.2.0.RC1/org/springframework/beans/factory/support/DefaultSingletonBeanRegistry.html)
3. [Does Spring need a third-level cache to solve circular dependencies](https://bestcodepaper.com/does-spring-need-a-third-level-cache-to-solve-circular-dependencies/#:~:text=L3%20cache%20The%20core%20of%20Spring%27s%20solution%20to,table%20is%20a%20description%20of%20the%20L3%20cache%3A)
4. [Lazy doc](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Lazy.html)
