---
title: CPU steal time
author: "-"
date: 2019-09-03T10:06:04+00:00
url: /?p=14896
categories:
  - Inbox
tags:
  - reprint
---
## CPU steal time

Steal Time(st)，用来衡量被虚拟机监视器(Hypervisor)偷去给其它虚拟机使用的 CPU 时间所占的比例。

[https://blog.csdn.net/jessysong/article/details/73571878](https://blog.csdn.net/jessysong/article/details/73571878)

Netflix 很关注 CPU 的 Steal Time。他们的策略是: 如果是当前虚拟机的 Steal Time 超过了你们设置的阈值，他们会关闭这台虚拟机并且在另外一台物理机上面重启。

如果你想要部署虚拟环境 (例如: Amazon EC2) ， steal time 就是你想要关注的性能指标之一。 如果这个指标的数值很高，那么说明机器状态非常糟糕。什么是 steal time？什么会引发高 steal time？多少才是警戒值 (你需要做什么) ？

CPU Steal Time 的定义
  
From ibm:

Steal time is the percentage of time a virtual CPU waits for a real CPU while the hypervisor is servicing another virtual processor.
  
你的虚拟机 (VM) 会与虚拟环境的宿主机上的多个虚拟机实例共享物理资源。其中之一共享的就是CPU时间切片。如果你的VM的物理机虚拟比是1/4， 那么它的CPU使用率不会限制于25%的CPU时间切片－它能够使用超过它设置的虚拟比。 (有别于内存的使用，内存大小是严格控制的) 。

哪里可以看到CPU Steal Time?
  
你可以使用Linux 的 TOP 命令来看到实时的一些性能指标。CPU相关的其中一行内容如下:
  
top
  
两个你可能较为熟悉的是 %id(空闲 百分比) 和 %wa(I/O 等待 百分比)。 如果 %id 很低， 那么说明CPU的工作负载很大并且没有多少计算负载能力剩余。 如果 %wa 很高，则说明瓶 CPU 处于等待计算的状态，但是正在等待I/O活动的完成(类似 从数据库中获取存储在 磁盘上 的一行数据)。

%st (percent steal time)  是CPU展示的最后一个性能指标。

CPU Steal Time - 类比售票厅
  
假设你打算买了若干张最新的好莱坞大片的电影票，且有两条队伍等待买票和一个售票口:

Movie Theater

如果我们把 CPU steal time 性能指标 类比成 售票的过程， 那么过程就是如下:

0% Steal Time - 现在是礼拜三下午场: 售票口正在工作，先处理第一条队伍的电影观众，然后处理第二条，然后第一条，然后第二条，轮流进行。处理的很快，且没有人在等待。

50% Steal Time - 现在是礼拜五晚上:  在队伍中的一个人有一半的时间需要等待另一个在售票口的人完成卖票，而不能立刻买到票。卖票的时间更长了。

100% Steal Time - 现在是礼拜五晚上并且 现金出纳金 坏了: 所有人都在等待。

为什么高 Steal Time 会对web应用有更大的影响
  
如果有你在负载未满的物理机器上面运行一个长时间的计算任务，那么它可能会使用超过它额定的CPU切片 时间。过一段时间，可能其他的VMs可能也会需要超过它们额定量的CPU切片 时间，所以这个任务的执行会变慢。对于长时间计算任务而言之，这个情况可能并不是不能接受的: 它可能是会晚点一完成或者也可能更快的完成 (由于它能够使用更多的资源) 。

然后，这种情况能够时代web应用停止响应。对于实时任务，类似快速响应许多的web请求，性能下降到1/4会对请求队列执行对应备选逻辑—中断请求。

Steal Time远高于0的原因
  
这里有两种可能性:

你需要一个额定更多CPU资源的虚拟机 (你的虚拟机是问题)

物理机已经超卖了并且多个虚拟机之间在激烈的竞争资源 (你的虚拟机不是问题)

提示: 你不能通过看当前被影响的虚拟机实例的CPU性能指标来判断你所遇到的场景。 (1 or 2)  当你有很多的虚拟宿主机上分别都部署了相同职责的服务程序 (可能作为集群) 时，就比较容易知道自己遇到的问题了。

资源图片

是否 %st(CPU Steal Time Percentage) 在所有机器上面都上涨了？

这个意味着你的虚拟机在使用更多的CPU资源。你需要为你的虚拟起增加更多的CPU资源的配额。

是否%st(CPU Steal Time Percentage) 只在一部分机器上面陡峭增长？

这个意味着物理机器被超卖了。把你自己的虚拟机挪到另一个物理机器去吧。

所有，什么时候你应该担心？
  
一般的参考标准-如果steal time 超过了10%并且持续了20分钟，那么虚拟机就可能性能下降了

当这种情况发生:

关闭虚拟机并且挪到另一台物理机器上面

如果steal time维持在很高的数值， 那么增加CPU资源配额。

如果steal time维持在很高的数值， 联系你的虚拟机提供商。你的虚拟机提供商有可能在超卖物理机。
