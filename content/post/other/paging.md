---
title: 内存分页
author: "-"
date: 2012-09-19T07:35:13+00:00
url: paging
categories:
  - Java
tags:
  - reprint
---
## 内存分页

## 内存分页, paging

为什么要分页
分段的内存碎片太大，是计算中发展过程中尝试过的方案，现在的方案是内存分页，通过某种方式，将虚拟地址映射到物理地址，映射的关系是通过一张表实现的，也就是页表。

分页机制
分页机制的思想是:通过映射，可以使连续的线性地址与物理地址相关联，逻辑上连续的线性地址对应的物理地址可以不连续。 分页的作用 - 将线性地址转换为物理地址 - 用大小相同的页替换大小不同的段

一级页表  我们把一页的大小定义为4K,那么4G就有1M个页，在32位的保护模式下，地址都是32位二进制表示的，用20位二进制定位页表，剩余的12位表示4K里面的偏移。  分页机制打开前要将页表地址加载到控制寄存器CR3中，这个过程是打开页表之前，所以存储的是物理实际地址，每个页表项对应一个物理页，通过页表项就可以访问到实际的物理地址。由于这个过程是固定的，CPU中集成了这个硬件模块，即MMU中的页部件。

二级页表
为什么要二级页表
每个进程1M个页表，每个4字节，进程多了占用的内存还是很多的。

一般进程使用的内存是远低于全部虚拟内存的。二级模式只为进程实际使用的那些虚拟内存区分配页表，既提升了效率，也减少了内存的使用量。

作者：长安
链接：[https://www.zhihu.com/question/50796850/answer/654281605](https://www.zhihu.com/question/50796850/answer/654281605)
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

作者：Ideal
链接：[https://www.zhihu.com/question/50796850/answer/1449056116](https://www.zhihu.com/question/50796850/answer/1449056116)
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

我觉得这个问题下目前的回答并没有讲清楚分段和分页的区别，有的答案甚至有错误，我尝试给一个我认为正确的理解。要回答为什么计算机中的内存访问会有分段机制，最好的方式是回到当时首次出现分段机制的历史背景下去考虑这个问题。事实上，对于计算机领域的诸多问题，很多时候工程上的选择都是完全跟当时的历史背景联系紧密的。并且因为硬件软件两个方面在过去几十年中都经历了极速的甚至是面目全非的发展，所以如果你不回溯背景，可能永远没有办法真正理解这些选择，分段最开始是从Intel的8086CPU，受限于价格和技术水平，当时的CPU和寄存器的宽度仍然为16位。那个时候还没有演化成像现在这样如此复杂的内存管理模式，程序在访问内存的时候还是直接给出相应单元的实际物理地址。为了便利地实现多道程序并发运行，也就需要支持对各个程序进行重定位，因为如果不支持重定位，凡是涉及到内存访问的地方都需要将地址硬编码，进而必须把某个程序加载到内存的固定区间。有了分段机制，程序中只需要使用基于段的相对地址，然后更改段基址，就可以方便地对程序进行重定位。当然，在8086CPU中，分段除了服务于重定位的目的，还有其他的作用。具体地说，8086CPU的地址线宽度是20位，可寻址的最大内存空间是1MB，但寄存器这些都是16位， 它是通过段加偏移的方式生成20位的地址，从而实现对1MB内存空间的寻址的。我们经常谈的程序可执行文件的分段，例如代码段数据段这些，在最开始其实就是为了跟上面描述的硬件上的内存分段机制对应，并且逻辑上能够更清晰有序地构造程序的组织结构。所以，总结来说，分段其实是80*86系列CPU的legacy。对于现代操作系统来说，由于已经引入了分页，分段更多的是一种历史包袱，而不是能够提供多大实际作用的内存管理机制。比如，The Linux Programming Interface那本书里就谈到：Linux uses segmentation in a very limited way. In fact, segmentation and paging are somewhat redundant, because both can be used to separate the physical address spaces of processes: segmentation can assign a different linear address space to each process, while paging can map the same linear address space into different physical address spaces. 事实上，通过将 cs (代码段) 和 ds (数据段) 寄存器的值设为0，Linux实际没有使用分段而只使用了分页。因为这样内存管理会更加简单，并且由于像RISC架构的处理器只对分段提供了非常有限的支持，不使用分段会提高Linux操作系统在不同CPU架构上的可移植性。

### 按需调页

当进程要访问的页不在内存中时，会触发页错误陷阱中断，在中断处理函数中将要访问的也载入到物理内存中。

页错误陷阱的处理

检查进程的内部页表，确定引用是否合法；
若非法，终止进程；若合法，调入页面；
从空闲链表中找到一个空闲帧；
调度磁盘操作，将页调入刚分配的帧中；
修改内部表和页表，表示页已在内存中；
退出中断；
此时进程已经可以访问所需的页，就好像它的所有页都在内存中。

注意：在退出页错误陷阱后会重新执行上一条指令。

按需调页会出现两种极端情况：

所有页都不在内存中，进程每执行完一页就产生页错误；
一条指令可能会访问多个页，从而产生多个页错误；
在实际中这两种情况出现的较少，由于程序具有局部引用，这使得按需调页性能较为合理。

2. 性能分析
现定义有效访问时间如下：

有效的访问时间=(1-p) *ma + p* pf
其中p为页错误概率，ma为内存访问时间，pf为也错误时间。

假定：ma=200us, pf=8ms

则有：有效访问时间=200+7999800 * p

可知：按需调页的性能与页错误概率相关。

此外另一个提升性能的方法是引入交换空间，磁盘I/O到交换空间要比到文件系统要快，因此在进程开始时将文件复制到交换空间，从交换空间进行按需调页，通过降低内存访问时间来提高性能。

转载于:[https://my.oschina.net/u/2990965/blog/880187](https://my.oschina.net/u/2990965/blog/880187)
