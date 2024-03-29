---
title: 页缓存, Page Cache
author: "-"
date: 2011-10-29T08:30:33+00:00
url: page-cache
categories:
  - os
tags:
  - memory

---
## 页缓存, Page Cache

可以通过命令 getconf PAGE_SIZE 来获取页的大小：

```bash
sudo getconf PAGE_SIZE
```

两个逻辑单位：

- 页，内存操作的基本单位
- 磁盘块，磁盘操作的基本单位

我们知道文件一般存放在硬盘 (机械硬盘或固态硬盘）中，CPU 并不能直接访问硬盘中的数据，而是需要先将硬盘中的数据读入到内存中，然后才能被 CPU 访问。

由于读写硬盘的速度比读写内存要慢很多 (DDR4 内存读写速度是机械硬盘 500 倍，是固态硬盘的 200 倍），所以为了避免每次读写文件时，都需要对硬盘进行读写操作，Linux 内核使用 页缓存 (Page Cache） 机制来对文件中的数据进行缓存。

本文使用的 Linux 内核版本为：Linux-2.6.23

### DDR4 内存带宽

```r
DDR4 2133：17 GB/s
DDR4 2400：19.2 GB/s
DDR4 2666：21.3 GB/s
DDR4 3200：25.6 GB/s
```

### 机械硬盘带宽

5400转笔记本硬盘平均读写速度大致在60-90MB这个区间
7200转台式机硬盘大致在130-190MB区间，10000转的西数黑盘也在这个区间内
10000转和15000转台式机硬盘数据不详

[https://zhuanlan.zhihu.com/p/443104177](https://zhuanlan.zhihu.com/p/443104177)

## 什么是页缓存

为了提升对文件的读写效率，Linux 内核会以页大小 (4KB）为单位，将文件划分为多数据块。当用户对文件中的某个数据块进行读写操作时，内核首先会申请一个内存页 (称为 页缓存）与文件中的数据块进行绑定。

当用户对文件进行读写时，实际上是对文件的 页缓存 进行读写。所以对文件进行读写操作时，会分以下两种情况进行处理：

当从文件中读取数据时，如果要读取的数据所在的页缓存已经存在，那么就直接把页缓存的数据拷贝给用户即可。否则，内核首先会申请一个空闲的内存页 (页缓存），然后从文件中读取数据到页缓存，并且把页缓存的数据拷贝给用户。  
当向文件中写入数据时，如果要写入的数据所在的页缓存已经存在，那么直接把新数据写入到页缓存即可。否则，内核首先会申请一个空闲的内存页 (页缓存），然后从文件中读取数据到页缓存，并且把新数据写入到页缓存中。对于被修改的页缓存，内核会定时把这些页缓存刷新到文件中。

### radix 树

radix树：又名基数树，它使用键值 (key-value）对的形式来保存数据，并且可以通过键快速查找到其对应的值。内核以文件读写操作中的数据 偏移量 作为键，以数据偏移量所在的 页缓存 作为值，存储在 address_space 结构的 page_tree 字段中。

### buffer cache, 块缓存

Buffer cache 也叫块缓冲，是对物理磁盘上的一个磁盘块进行的缓冲，其大小为通常为1k，磁盘块也是磁盘的组织单位。设立 buffer cache 的目的是为在程序多次访问同一磁盘块时，减少访问时间。系统将磁盘块首先读入 buffer cache，如果 cache 空间不够时，会通过一定的策略将一些过时或多次未被访问的 buffer cache 清空。程序在下一次访问磁盘时首先查看是否在 buffer cache 找到所需块，命中可减少访问磁盘时间。不命中时需重新读入 buffer cache。对 buffer cache 的写分为两种，一是直接写，这是程序在写 buffer cache 后也写磁盘，要读时从 buffer cache 上读，二是后台写，程序在写完 buffer cache后并不立即写磁盘，因为有可能程序在很短时间内又需要写文件，如果直接写，就需多次写磁盘了。这样效率很低，而是过一段时间后由后台写，减少了多次访磁盘的时间。

Buffer cache 是由物理内存分配，Linux 系统为提高内存使用率，会将空闲内存全分给 buffer cache ，当其他程序需要更多内存时，系统会减少 cache 大小。

buffer cache 的内存，是块设备的读写缓冲区，更靠近存储设备，或者直接就是 disk 的缓冲区。

### page cache 和 Buffer cache 的区别

磁盘的操作有逻辑级 (文件系统）和物理级 (磁盘块），这两种Cache就是分别缓存逻辑和物理级数据的。

假设我们通过文件系统操作文件，那么文件将被缓存到 Page Cache，如果需要刷新文件的时候，Page Cache 将交给 Buffer Cache 去完成，因为 Buffer Cache 就是缓存磁盘块的。

也就是说，直接去操作文件，那就是 Page Cache 区缓存，用 dd 等命令直接操作磁盘块，就是 Buffer Cache 缓存的东西。

Page cache 实际上是针对文件系统的，是文件的缓存，在文件层面上的数据会缓存到 page cache。文件的逻辑层需要映射到实际的物理磁盘，这种映射关系由文件系统来完成。当 page cache 的数据需要刷新时，page cache 中的数据交给 buffer cache，但是这种处理在 2.6 版本的内核之后就变的很简单了，没有真正意义上的 cache 操作。

Buffer cache 是针对磁盘块的缓存，也就是在没有文件系统的情况下，直接对磁盘进行操作的数据会缓存到 buffer cache中，例如，文件系统的元数据都会缓存到 buffer cache中。

简单说来，page cache 用来缓存文件数据，buffer cache 用来缓存磁盘数据。在有文件系统的情况下，对文件操作，那么数据会缓存到 page cache，如果直接采用dd等工具对磁盘进行读写，那么数据会缓存到buffer cache。

Buffer(Buffer Cache) 以块形式缓冲了块设备的操作，定时或手动的同步到硬盘，它是为了缓冲写操作然后一次性将很多改动写入硬盘，避免频繁写硬盘，提高写入效率。

Cache(Page Cache) 以页面形式缓存了文件系统的文件，给需要使用的程序读取，它是为了给读操作提供缓冲，避免频繁读硬盘，提高读取效率。

>[https://lday.me/2019/09/09/0023_linux_page_cache_and_buffer_cache/](https://lday.me/2019/09/09/0023_linux_page_cache_and_buffer_cache/)
>[https://cloud.tencent.com/developer/article/1848933](https://cloud.tencent.com/developer/article/1848933)
>[https://qinglinmao8315.github.io/linux/2018/03/14/linux-page-cache.html](https://qinglinmao8315.github.io/linux/2018/03/14/linux-page-cache.html)
>[https://zhuanlan.zhihu.com/p/35277219](https://zhuanlan.zhihu.com/p/35277219)
