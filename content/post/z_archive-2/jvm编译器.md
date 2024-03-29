---
title: JVM编译器
author: "-"
date: 2017-03-30T06:06:29+00:00
url: /?p=9988
categories:
  - Inbox
tags:
  - reprint
---
## JVM编译器

即时编译器

Java虚拟机中内置了两个即时编译器,分别为Client和Server或则叫C1和C2.
  
C1编译器将字节码编译为本地代码,进行简单可靠的优化,C2编译器则会启动一些编译耗时较长的优化,甚至会根据性能监控信息进行一些不可靠的激进优化。C1和C2都是编译一些热点代码 (多次调用的方法或者多次执行的循环体) ,因此在编译前,首先要进行热点探测,HotSpot虚拟机中使用的是基于计数器的热点探测方法。
  
它为每个方法都准备了两个计数器,方法调用计数器和回边计数器。
  
方法调用计数器统计的并不是方法被调用的绝对次数,而是一个相对的执行频率,即一段时间内方法被调用的次数。当超过一定的时间限度,如果方法调用次数仍然不足以让它提交给即时编译器,那这个方法的调用计数器就会衰减一般,这个过程称为方法调用计数器热度的衰减。
  
回边计数器统计的是一个方法中循环体代码执行的次数,它没有热度衰减。建立回边计数器统计的目的是为了处罚OSR编译,OSR即栈上替换,也就是编译发生在方法执行过程之中。

[http://blog.csdn.net/tingfeng96/article/details/52261219](http://blog.csdn.net/tingfeng96/article/details/52261219)

[http://blog.csdn.net/zhuyijian135757/article/details/38391785](http://blog.csdn.net/zhuyijian135757/article/details/38391785)
  
[https://cyberdak.github.io/jvm/2017/03/25/jvm-restart-cause-high-load](https://cyberdak.github.io/jvm/2017/03/25/jvm-restart-cause-high-load)
