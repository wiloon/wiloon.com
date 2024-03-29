---
title: awt swing
author: "-"
date: 2014-11-30T01:40:15+00:00
url: /?p=7055
categories:
  - Inbox
tags:
  - reprint
---
## awt swing
简单的说:

AWT 是抽象窗口组件工具包，是 java 最早的用于编写图形节目应用程序的开发包。
  
Swing 是为了解决 AWT 存在的问题而新开发的包，它以 AWT 为基础的。

具体的说就是: 

AWT 是Abstract Window ToolKit (抽象窗口工具包)的缩写，这个工具包提供了一套与本地图形界面进行交互的接口。AWT 中的图形函数与操作系统所提供的图形函数之间有着一一对应的关系，我们把它称为peers。 也就是说，当我们利用 AWT 来构件图形用户界面的时候，我们实际上是在利用操作系统所提供的图形库。由于不同操作系统的图形库所提供的功能是不一样的，在一个平台上存在的功能在另外 一个平台上则可能不存在。为了实现Java语言所宣称的"一次编译，到处运行"的概念，AWT 不得不通过牺牲功能来实现其平台无关性，也就是说，AWT 所提供的图形功能是各种通用型操作系统所提供的图形功能的交集。由于AWT 是依靠本地方法来实现其功能的，我们通常把AWT控件称为重量级控件。

Swing 是在AWT的基础上构建的一套新的图形界面系统，它提供了AWT 所能够提供的所有功能，并且用纯粹的Java代码对AWT 的功能进行了大幅度的扩充。例如说并不是所有的操作系统都提供了对树形控件的支持， Swing 利用了AWT 中所提供的基本作图方法对树形控件进行模拟。由于 Swing 控件是用100%的Java代码来实现的，因此在一个平台上设计的树形控件可以在其他平台上使用。由于在Swing 中没有使用本地方法来实现图形功能，我们通常把Swing控件称为轻量级控件。

AWT和Swing之间的基本区别: AWT 是基于本地方法的C/C++程序，其运行速度比较快；Swing是基于AWT 的Java程序，其运行速度比较慢。对于一个嵌入式应用来说，目标平台的硬件资源往往非常有限，而应用程序的运行速度又是项目中至关重要的因素。在这种矛 盾的情况下，简单而高效的AWT 当然成了嵌入式Java的第一选择。而在普通的基于PC或者是工作站的标准Java应用中，硬件资源对应用程序所造成的限制往往不是项目中的关键因素，所 以在标准版的Java中则提倡使用Swing， 也就是通过牺牲速度来实现应用程序的功能。