---
title: Lua
author: "-"
date: 2013-02-13T09:39:46+00:00
url: lua
categories:
  - CS
tags:
  - reprint
---
## Lua

[http://zh.wikipedia.org/zh-cn/Lua](http://zh.wikipedia.org/zh-cn/Lua)

[http://baike.baidu.com/view/416116.htm](http://baike.baidu.com/view/416116.htm)

**Lua** ( /ˈluːə/ ) 程序设计语言是一个简洁、轻量、可扩展的脚本语言，是葡萄牙语中"Luna" (月亮) 的意思。

Lua 是一个动态弱类型语言，支援增量式垃圾收集策略。有内建的，与操作系统无关的协作式多线程 (coroutine) 支持

Lua的目标是成为一个很容易**嵌入**其它语言中使用的语言。大多数程序员也认为它的确做到了这一点。

很多应用程序使用Lua作为自己的嵌入式脚本语言，以此来实现可配置性、可扩展性。这其中包括大话西游II、仙境传说、魔兽世界、战锤40k、博德之门、轩辕剑外传汉之云、愤怒的小鸟等。

Lua是一种**轻量**语言，它的官方版本只包括一个精简的核心和最基本的库。这使得Lua体积小、启动速度快。它用标准C语言编写并以源 代码形式开放，编译后仅仅一百余K，可以很方便的嵌入别的程式里。和许多"大而全"的语言不一样，网路通讯、图形界面等都没有默认提供。但是Lua可以很 容易地被扩展: 由宿主语言 (通常是C或C++) 提供这些功能，Lua可以使用它们，就像是本来就内置的功能一样。事实上，现在已经有很多成熟的扩展模块可供选用。

Lua是一种多重编程范式的程式设计语言: 它只提供了很小的一个特性集合来满足不同编辑范式的需要，而不是为某种特定的编辑范式提供繁杂的特性支援。例如，Lua并不提供继承这个特性，但是你可以用元表来模拟它。诸如名字空间、类这些概念都没有在语言基本特性中实现，但是我们可以用表结构 (Lua唯一提供的复杂数据结构) 轻易模拟。Lua可以在运行时随时构造出一个函数，并把它看作一个对象 (正是所谓的first class function) ，这个特性可以很好的满足函数式编程的需要。这是提供了这些基本的元特性，我们可以任意的对语言进行自需的改造。

Lua 原生支援的数据类型非常之少，它只提供了数字 (缺省是双精度浮点数，可配置) 、布尔量、字符串、表、子程序、协程 (coroutine) 以及用户自定义数据这几种。但是其处理表和字符串的效率非常之高，加上元表的支援，我们可以高效的模拟出需要的复杂数据类型 (比如集合、数组等) 。
