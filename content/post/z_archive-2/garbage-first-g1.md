---
title: Garbage-First G1
author: "-"
date: 2017-06-04T07:11:24+00:00
url: /?p=10468
categories:
  - Inbox
tags:
  - reprint
---
## Garbage-First G1

[http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/](http://ifeve.com/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3g1%E5%9E%83%E5%9C%BE%E6%94%B6%E9%9B%86%E5%99%A8/)

G1 GC是Jdk7的新特性之一、Jdk7+版本都可以自主配置G1作为JVM GC选项；作为JVM GC算法的一次重大升级、DK7u后G1已相对稳定、且未来计划替代CMS、所以有必要深入了解下:

不同于其他的分代回收算法、G1将堆空间划分成了互相独立的区块。每块区域既有可能属于O区、也有可能是Y区,且每类区域空间可以是不连续的 (对比CMS的O区和Y区都必须是连续的) 。这种将O区划分成多块的理念源于: 当并发后台线程寻找可回收的对象时、有些区块包含可回收的对象要比其他区块多很多。虽然在清理这些区块时G1仍然需要暂停应用线程、但可以用相对较少的时间优先回收包含垃圾最多区块。这也是为什么G1命名为Garbage First的原因: 第一时间处理垃圾最多的区块。

平时工作中大多数系统都使用CMS、即使静默升级到JDK7默认仍然采用CMS、那么G1相对于CMS的区别在:

G1在压缩空间方面有优势
  
G1通过将内存空间分成区域 (Region) 的方式避免内存碎片问题
  
Eden, Survivor, Old区不再固定、在内存使用效率上来说更灵活
  
G1可以通过设置预期停顿时间 (Pause Time) 来控制垃圾收集时间避免应用雪崩现象
  
G1在回收内存后会马上同时做合并空闲内存的工作、而CMS默认是在STW (stop the world) 的时候做
  
G1会在Young GC中使用、而CMS只能在O区使用
  
就目前而言、CMS还是默认首选的GC策略、可能在以下场景下G1更适合:

服务端多核CPU、JVM内存占用较大的应用 (至少大于4G)
  
应用在运行过程中会产生大量内存碎片、需要经常压缩空间
  
想要更可控、可预期的GC停顿周期；防止高并发下应用雪崩现象
