---
layout: default
tags: concurrency
title: 并发编程学习笔记4-对象的组合
---

#并发编程学习笔记4-对象的组合#

摘录于[《Java并发编程实战》](http://book.douban.com/subject/10484692/)

##设计线程安全的类##

在设计线程安全类的过程中，需要包含以下三个基本要素：

* 找出构成对象状态的所有变量。
* 找出约束状态变量的不变性条件。
* 建立对象状态的并发访问管理策略。

对于含有 N 个基本类型域的对象，其状态就是这些域构成的 N 元组。如果在对象的域中引用了其他对象，那么该对象的状态将包含被引用对象的域。

同步策略（Synchronization Policy）定义了如何在不违背对象不变性条件或后验条件的情况下对其状态的访问操作进行协同。同步策略规定了如何将不可变性、线程封闭与加锁机制等结合起来以维护线程的安全性，并且还规定了哪些变量由哪些锁来保护。

###收集同步需求###

对象与变量都有一个状态空间，即所有可能的取值。在许多类中都定义了一些不可变条件，用于判断状态是有效的还是无效的。同样，在操作中还会包含一些后验条件来判断状态迁移是否是有效的。当下一个状态需要依赖当前状态时，这个操作就必须是一个复合操作。

由于不变性条件以及后验条件在状态及状态转换上施加了各种约束，因此就需要额外的封装与同步。

如果在一个不变性条件中包含多个变量，那么在执行任何访问相关变量的操作时，都必须持有保护这些变量的锁。

###依赖状态的操作###

如果在某个操作中包含有基于状态的先验条件（Precondition），那么这个操作就被称为依赖状态的操作。

在 Java 中，等待某个条件为真的各种内置机制（包括等待和通知机制）都与内置加锁机制紧密关联。Java 提供了一些阻塞类，例如 `BlockingQueue` 、`Semaphore` 以及其他的同步工具类。

###状态的所有权###

在定义哪些变量将构成对象的状态时，只考虑对象拥有的数据。所有权（Ownership）在 Java 中并没有得到充分的体现，而是属于类设计中的一个要素。

许多情况下，对象封装它拥有的状态，对它封装的状态拥有所有权。如果发布了某个可变对象的引用，那么就不再拥有独占的控制权，最多是“共享控制权”。

容器类通常表现出一种“所有权分离”的形式，其中容器类拥有其自身的状态，而客户代码则拥有容器中各个对象的状态。在 `ServletContext` 中可以通过名称来注册（`setAttribute`)或获取(`getAttribute`)应用程序对象。当调用 `setAttribute` 和 `getAttribute` 时，`Servlet` 不需要同步，但当使用保存在 `ServletContext` 中的对象时，则可能需要同步。这些对象由应用程序拥有，`Servlet` 容器只是替引用程序保管它们。
