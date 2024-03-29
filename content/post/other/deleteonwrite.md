---
title: Linux 下删除正在进行读写操作的文件
author: "-"
date: 2015-04-22T04:30:58+00:00
url: deleteonwrite
categories:
  - Linux
tags:
  - reprint
---
## Linux 下删除正在进行读写操作的文件

多进程环境下, 打开同一个文件, 进行读写操作过程中, 如果其中一个进程删除这个文件, 那么, 另外正在读写这个文件会发生什么呢？

因为文件被删除了, 正在读写的进程发生异常？
正在读写的进程仍然正常读写, 好像没有发现发生了什么？

删除文件，其实是删除 inode 和文件的对应关系，而 linux 文件系统只有当连接文件的 inode 数量为0，并且没有程序在使用这个文件时，才会标记文件内容所在的数据块删除。

每个文件都有 2 个 link 计数器 - i_count 和 i_nlink。i_count 的意义是当前使用者的数量，i_nlink 的意义是介质连接的数量；或者可以理解为 i_count 是内存引用计数器，i_nlink 是硬盘引用计数器。再换句话说，当文件被某个进程引用时，i_count 就会增加；当创建文件的硬连接的时候，i_nlink 就会增加。

对于 rm 而言，就是减少 i_nlink。这里就出现一个问题，如果一个文件正在被某个进程调用，而用户却执行 rm 操作把文件删除了，会出现什么结果呢？

当用户执行 rm 操作后，ls 或者其他文件管理命令不再能够找到这个文件，但是进程却依然在继续正常执行，依然能够从文件中正确的读取内容。这是因为，rm 操作只是将 i_nlink 置为 0 了；由于文件被进程引用的缘故，i_count 不为 0，所以系统没有真正删除这个文件。i_nlink 是文件删除的充分条件，而 i_count 才是文件删除的必要条件。

基于以上只是，大家猜一下，如果在一个进程在打开文件写日志的时候，手动或者另外一个进程将这个日志删除，会发生什么情况？

是的，数据库并没有停掉。虽然日志文件被删除了，但是有一个进程已经打开了那个文件，所以向那个文件中的写操作仍然会成功，数据仍然会提交。

下面，告诉大家如何恢复那个删除的文件。

例如，你删除了 tcpdump.log, 执行 lsof | grep tcpdump.log，你应该能看到这样的输出：

tcpdump 2864 tcpdump 4w REG 253,0 0 671457 /root/tcpdump.log (deleted)

然后：

cp /proc/2864/fd/4  /root/tcpdump.log

rm 命令用的是 unlink() 系统调用
当文件正在被进程打开时，执行 unlink() 只会删除文件名，并不会删除文件内容，只有所有打开此文件的进程都关闭此文件后（注意当进程退出时，会自动关闭所有打开的文件），文件内容才会被真正删除。

文件 在相应目录中已经被删除，但是由于有其他进程打开这一文件后还未关闭，操作系统其实还为这些进程保留了磁盘上被打开的文件内容。

### 删除正在使用的文件——釜底抽薪？

[https://zhuanlan.zhihu.com/p/25650525](https://zhuanlan.zhihu.com/p/25650525)

[https://zhuanlan.zhihu.com/p/25600743](https://zhuanlan.zhihu.com/p/25600743)  
[http://www.forthxu.com/blog/article/40.html](http://www.forthxu.com/blog/article/40.html)  

## 移动正在写入的文件, mv command

如果是在同一个设备相同分区类型, 那么，inode和文件的内容都不需要改变, 只有文件名与inode之间的对应关系改变了, 也就是文件所在的**目录**的 data block 发生了变化, 因此，程序依然能通过原先的 data block读写取数据。

这种情况  mv 的实现是调用 rename 函数, rename 只修改文件名称，不会影响已有的打开这个文件的进程的读写操作；写入的内容会保存在重命名后的文件里。

如果不在同一个设备或相同的分区类型, mv 操作实际是先将原有文件里的内容copy到新的文件里，然后调用unlink函数；unlink时如果该文件在被使用，不会将文件真正删除，只会将文件名改为空，在所有的对该文件的操作都完成并调用close后该文件才会被真正删除；这种情况下对原有文件的写入操作不会保存在新的文件里。

当使用mv来rename文件时，如果在同一文件系统，那么，inode和文件的内容都不需要改变，只有文件名与inode之间的对应关系改变了，也就是文件所在的目录的数据block发生了变化。 因此，程序依然能通过原先的数据block读取数据。

[https://zhuanlan.zhihu.com/p/449474246](https://zhuanlan.zhihu.com/p/449474246)

## 复制正在写入的文件, cp command

目标文件不存在时

当dest.txt不存在时，执行cp src.txt dest.txt。
可以发现dest.txt和src.txt的inode不一样，也就是用open()新建一个文件dest.txt，然后读取src.txt的数据再写入dest.txt。

cp前：

src.txt: Inode: 34643179
cp后：

dest.txt: Inode: 34257722


目标文件存在时
此时dest.txt已经存在，再次执行cp src.txt dest.txt。
可以发现dest.txt跟上次执行的dest.txt的inode没有变化，同时看open()的参数也可以看出：先清空了dest.txt的内容，再把新的内容写入目标文件。没有文件的删除和创建，所以inode没有变化。

cp前：

src.txt: Inode: 34643179
dest.txt: Inode: 34257722
cp后：

dest.txt: Inode: 34257722

当目标文件已经存在时，cp 命令会把原目标文件内容清空然后再写入新内容。如果文件是可执行文件，一旦成功写入新内容，必然会影响当前正在运行的进程，因而操作系统禁止了对可执行文件的写入操作。但对于非可执行文件，操作系统并不禁止，所以，非可执行文件会被更新成新的内容，并被原先的程序读取到，但这可能会带来意料之外的结果。

## rm command

在Linux中，要真正删除一个文件，需要满足两个条件：

链接数为0
没有进程打开该文件
系统调用unlink()是移除目标文件的一个链接。可以发现r

## unlink系统调用

从文件系统中删除一个名称。如果名称是文件的最后一个连接，并且没有其它进程将文件打开，名称对应的文件会实际被删除。
如果文件仍旧是打开的，或者是被进程占用，其内容不会被删除。只有当进程关闭该文件或终止时(这种情况下，内核关闭该进程所打开的全部文件)，该文件的内容才会被删除。

所以你可能会遇到，一个进程在读写文件时，你发现磁盘空间不足，使用rm删除文件，却发现磁盘空间却没有释放的情况。 使用lsof | grep deleted可以查看占用的进程。kill进程之后，文件才能真正的被删除。

[https://github.com/Kevin-fqh/learning-k8s-source-code/blob/master/Linux/(07)cp%E3%80%81mv%E3%80%81rm%E7%9A%84%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0.md](https://github.com/Kevin-fqh/learning-k8s-source-code/blob/master/Linux/(07)cp%E3%80%81mv%E3%80%81rm%E7%9A%84%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0.md)
