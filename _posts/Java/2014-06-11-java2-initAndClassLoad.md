---
layout: default
tags: java
title: Java 学习笔记(2)--初始化和类的加载
---

#Java 学习笔记(2)--初始化和类的加载#

摘录于[《Java编程思想》](http://book.douban.com/subject/2130190/)

每个类的编译代码都存在于它自己的独立的文件中。该文件只在需要使用程序代码时才会被加载。这通常是指加载发生于创建类的第一个对象之时，但是当访问 static 域或 static 方法时，也会发生加载。构造器也是static方法。

所有的的 static 对象和 static 代码段都会在加载时依程序中的顺序（即，定义类时的书写顺序）而依次初始化。

##继承与初始化##

1. 加载器加载子类的编译代码后，会继续加载基类的编译代码，注意，加载器加载子类的编译代码的时机是子类任何 static 成员被访问。

2. 接下来，根基类中的 static 初始化即会被执行，然后是下一个导出类的 static。这种方式很重要，**因为导出类的 static 初始化可能会依赖于基类成员能否被正确初始化**。

3. 然后，就可以创建对象了。
	* 首先，为对象分配足够的存储空间。
	* 接着，对象中所有基本类型都会被设为默认值，对象引用被设为null。
	* 然后，执行所有出现于字段处的初始化动作（按照书写顺序）。
	* 最后，执行构造器，在这之中会先执行基类的构造器（如果存在继承的话）。
