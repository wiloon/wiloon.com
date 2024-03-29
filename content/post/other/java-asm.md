---
title: java asm
author: "-"
date: 2012-12-15T14:16:51+00:00
url: /?p=4904
categories:
  - Java
tags:
  - reprint
---
## java asm

什么是 ASM ？

ASM 是一个 Java 字节码操控框架。它能被用来动态生成类或者增强既有类的功能。ASM 可以直接产生二进制 class 文件，也可以在类被加载入 Java 虚拟机之前动态改变类行为。Java class 被存储在严格格式定义的 .class 文件里，这些类文件拥有足够的元数据来解析类中的所有元素: 类名称、方法、属性以及 Java 字节码 (指令) 。ASM 从类文件中读入信息后，能够改变类行为，分析类信息，甚至能够根据用户要求生成新类。

与 BCEL 和 SERL 不同，ASM 提供了更为现代的编程模型。对于 ASM 来说，Java class 被描述为一棵树；使用 Visitor模式(访问者模式) 遍历整个二进制结构；事件驱动的处理方式使得用户只需要关注于对其编程有意义的部分，而不必了解 Java 类文件格式的所有细节: ASM 框架提供了默认的 "response taker"处理这一切。

为什么要动态生成 Java 类？

动态生成 Java 类与 AOP 密切相关的。AOP 的初衷在于软件设计世界中存在这么一类代码，零散而又耦合: 零散是由于一些公有的功能 (诸如著名的 log 例子) 分散在所有模块之中；同时改变 log 功能又会影响到所有的模块。出现这样的缺陷，很大程度上是由于传统的 面向对象编程注重以继承关系为代表的"纵向"关系，而对于拥有相同功能或者说方面  (Aspect) 的模块之间的"横向"关系不能很好地表达。例如，目前有一个既有的银行管理系统，包括 Bank、Customer、Account、Invoice 等对象，现在要加入一个安全检查模块， 对已有类的所有操作之前都必须进行一次安全检查。

[http://www.ibm.com/developerworks/cn/java/j-lo-asm30/](http://www.ibm.com/developerworks/cn/java/j-lo-asm30/)
