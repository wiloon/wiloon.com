---
title: 协程, Coroutine
author: "-"
date: 2015-02-04T02:27:10+00:00
url: Coroutine
categories:
  - OS
tags:
  - reprint
  - Coroutine
---
## 协程, Coroutine

协程别名: 微线程，纤程。英文:Coroutine, Green threads, fibers

传统编程语言中子程序或者函数是层级调用的,函数可以调用其它函数, 调用者需要等待被调用者结束之后继续执行, 函数调用是通过栈实现的. 一个线程就是按顺序执行一个或几个子函数, 函数调用只有一个入口和一个出口.  
协程看上去也是函数,但是执行过程中在子程序内部可以中断,然后执行别的函数, 然后再被调度回来执行.

- 协程比线程有更高的执行效率, 协程没有线程切换的开销
- 协程在用户空间调度, 不涉及系统调用或任何阻塞调用, 不需要用来守卫关键区块的同步性原语（primitive）比如互斥锁、信号量等，并且不需要来自操作系统的支持
- 协程不需要多线程的锁机制, 为只有一个线程，也不存在同时写变量冲突，在协程中控制共享资源不加锁，只需要判断状态就好了，所以执行效率比多线程高很多。
- 协程是协作式多任务的, 线程典型是抢占式多任务的
- 因为协程是一个线程执行，那怎么利用多核CPU呢？最简单的方法是多进程+协程，既充分利用多核，又充分发挥协程的高效率，可获得极高的性能。

使用抢占式调度的线程实现协程，但是会失去某些利益（特别是对硬性实时操作的适合性和相对廉价的相互之间切换）。

协程是语言层级的构造，可看作一种形式的控制流，而线程是系统层级的构造

## 生成器

生成器，也叫作“半协程”[8]，是协程的子集。

[https://www.liaoxuefeng.com/wiki/1016959663602400/1017968846697824](https://www.liaoxuefeng.com/wiki/1016959663602400/1017968846697824)

[https://zh.wikipedia.org/wiki/%E5%8D%8F%E7%A8%8B](https://zh.wikipedia.org/wiki/%E5%8D%8F%E7%A8%8B)

## 有栈协程

有栈协程的好处，由于栈帧可以直接完全保存运行期上下文（主要是寄存器值），因此可以在任何时刻暂停协程的运行，这就很方便地支持了抢占式的调度器。

## 无栈协程

有栈协程的好处，由于栈帧可以直接完全保存运行期上下文（主要是寄存器值），因此可以在任何时刻暂停协程的运行，这就很方便地支持了抢占式的调度器。而无栈协程的上下文是一般通过类似结构体的方式保存在内存中，它依赖使用者显式地切换协程，否则协程不会主动让出执行权。

另外，有栈协程更方便将同步代码改造为异步代码，就像我们的例子一样，只需改动一行，加上go关键字就可以了。而无栈协程，同步改造为异步则更为复杂，甚至会导致牵一发动全身（async关键字扩散问题）。

Rust无栈协程
既然已经有了有栈协程，那么无栈协程是否还有优势呢。答案肯定的！

通常，无栈协程在内存空间和协程上下文切换的效率更高。值得说明的是，无栈协程并不是说不需要运行时的栈空间，而是和协程的创建者共用同一块运行时的栈空间。

如果一定要用一句话概括无栈协程，那就是：无栈协程可以看做是有状态的函数（generator），每次执行时会根据当前的状态和输入参数，得到（generate）输出，但不一定为最终结果。
