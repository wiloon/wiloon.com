---
title: java8 去除 永久代, metaspace (元空间)
author: "-"
date: 2017-06-02T02:35:42+00:00
url: metaspace
categories:
  - Inbox
tags:
  - reprint
---
## java8 去除 永久代, metaspace (元空间)

在过去 (当自定义类加载器使用不普遍的时候) ,类几乎是"静态的"并且很少被卸载和回收, 因此类也可以被看成"永久的"。另外由于类作为JVM实现的一部分,它们不由程序来创建,因为它们也被认为是"非堆"的内存。

在JDK8之前的 HotSpot虚拟机中,类的这些"永久的"数据存放在一个叫做永久代的区域。永久代一段连续的内存空间,我们在JVM启动之前可以通过设置-XX:MaxPermSize 的值来控制永久代的大小, 32位机器默认的永久代的大小为64M, 64位的机器则为85M。永久代的垃圾回收和老年代的垃圾回收是绑定的,一旦其中一个区域被占满, 这两个区都要进行垃圾回收。但是有一个明显的问题, 由于我们可以通过‑XX:MaxPermSize 设置永久代的大小,一旦类的元数据超过了设定的大小, 程序就会耗尽内存,并出现内存溢出错误(OOM)。

备注: 在JDK7之前的 HotSpot虚拟机中, 纳入字符串常量池的字符串被存储在永久代中, 因此导致了一系列的性能问题和内存溢出错误。

>[http://www.cnblogs.com/moonandstar08/p/5001914.html](http://www.cnblogs.com/moonandstar08/p/5001914.html)

这项改动是很有必要的, 因为对永久代进行调优是很困难的。 永久代中的元数据可能会随着每一次 Full GC发生而进行移动。并且为永久代设置空间大小也是很难确定的, 因为这其中有很多影响因素,比如类的总数,常量池的大小和方法数量等。

同时,HotSpot 虚拟机的每种类型的垃圾回收器都需要特殊处理永久代中的元数据。将元数据从永久代剥离出来, 不仅实现了对元空间的无缝管理, 还可以简化Full GC以及对以后的并发隔离类元数据等方面进行优化。

### 新增加的 metaspace (元空间)
  
持久代的空间被彻底地删除了, 它被一个叫元空间的区域所替代了。 持久代删除了之后,很明显, JVM会忽略 PermSize 和 MaxPermSize 这两个参数,还有就是你再也看不到 java.lang.OutOfMemoryError: PermGen error 的异常了。

JDK 8 的 HotSpot JVM 现在使用的是本地内存来表示类的元数据,这个区域就叫做元空间。

元空间存储类的元信息，静态变量和常量池等并入堆中。相当于永久代的数据被分到了堆和元空间中。

元空间的特点:

- 充分利用了Java语言规范中的好处: 类及相关的元数据的生命周期与类加载器的一致。
- 每个加载器有专门的存储空间
- 只进行线性分配
- 不会单独回收某个类
- 省掉了GC扫描及压缩的时间
- 元空间里的对象的位置是固定的
- 如果GC发现某个类加载器不再存活了,会把相关的空间整个回收掉

#### 元空间的内存分配模型

- 绝大多数的类元数据的空间都从本地(native)内存中分配
- 用来描述类元数据的类也被删除了
- 分元数据分配了多个虚拟内存空间
- 给每个类加载器分配一个内存块的列表。块的大小取决于类加载器的类型; sun/反射/代理对应的类加载器的块会小一些
- 归还内存块,释放内存块列表
- 一旦元空间的数据被清空了,虚拟内存的空间会被回收掉

减少碎片的策略

[http://www.infoq.com/cn/articles/Java-PERMGEN-Removed](http://www.infoq.com/cn/articles/Java-PERMGEN-Removed)
  
[http://www.jianshu.com/p/7b88aa16c2f6](http://www.jianshu.com/p/7b88aa16c2f6)
  
[http://www.cnblogs.com/paddix/p/5309550.html](http://www.cnblogs.com/paddix/p/5309550.html)
