---
title: fsck
author: "-"
date: 2017-08-01T07:17:36+00:00
url: /?p=10965
categories:
  - Inbox
tags:
  - reprint
---
## fsck

    fsck
  
[https://man.linuxde.net/fsck/embed#?secret=oLlAnrAniE](https://man.linuxde.net/fsck/embed#?secret=oLlAnrAniE)

fsck命令文件系统管理 fsck命令被用于检查并且试图修复文件系统中的错误。当文件系统发生错误四化,可用fsck指令尝试加以修复。 语法 fsck(选项)(参数) 选项
  
-f 即使文件系统标记为 clean 也强制进行检查
  
-b superblock 使用替代的超级块
  
-y 对所有问题都回答 "yes"
  
-a: 自动修复文件系统,不询问任何问题；
  
-A: 依照/etc/fstab配置文件的内容,检查文件内所列的全部文件系统；
  
-N: 不执行指令,仅列出实际执行会进行的动作；
  
-P: 当搭配"-A"参数使用时,则会同时检查所有的文件系统；
  
-r: 采用互动模式,在执行修复时询问问题,让用户得以确认并决定处理方式；
  
-R: 当搭配"-A"参数使用时,则会略过/目录的文件系统不予检查；
  
-s: 依序执行检查作业,而非同时执行；
  
-t<文件系统类型>: 指定要检查的文件系统类型；
  
-T: 执行fsck指令时,不显示标题信息；
  
-V: 显示指令执行过程。 参数 文件系统: 指定要查看信息的文件系统。 实例 linux的文件系统损坏会导致linux不正常关机,出错的时候如果系统告诉你是哪一块硬盘的分区有问题,比如是/dev/hda2,接着用如下的命令去对付它:  fsck -y /dev/hda2 结束后使用reboot命令重启系统这样就好了！ 如果不知道时哪个地方出了问题,可以直接:  fsck 在随后的多个确认对话框中输入:y 结束后同样使用reboot命令重启系统这样就好了！

来自: [http://man.linuxde.net/fsck](http://man.linuxde.net/fsck)

fsck命令文件系统管理 fsck命令被用于检查并且试图修复文件系统中的错误。当文件系统发生错误四化,可用fsck指令尝试加以修复。

来自: [http://man.linuxde.net/fsck](http://man.linuxde.net/fsck)
