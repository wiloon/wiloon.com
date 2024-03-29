---
title: Awesome
author: "-"
date: 2012-11-30T13:50:30+00:00
url: /?p=4818
categories:
  - Linux
tags:
  - reprint
---
## Awesome
# 

[http://confusedfly.yo2.cn/articles/awesome%E7%AE%80%E4%BB%8B.html](http://confusedfly.yo2.cn/articles/awesome%E7%AE%80%E4%BB%8B.html)

# 什么是平铺窗口管理器

这里首先需要解释一下窗口管理器(Windows Manager)和桌面环境(Desktop Environment)的概念。窗口管理器负责绘制窗口的边框,处理窗口运行比如移动、最小化之类的行为。
而桌面环境则是窗口管理器的超集,它使用窗口管理器及其其他软件提供一个完整的工作环境。比如说,gnome就是一个桌面环境,默认使用metacity作为窗口管理器。

那么什么是平铺式窗口管理器？简单来说,所谓的平铺就是之所有的窗口都不会相互重叠,而是自动的被调整大小使得它们能够刚好占满整个屏幕。这和传统的桌面环境的概念相差很大,也许你会疑问这样的环境下如何工作？

事实上对于Linux这种需要大量使用命令行的操作系统来说平铺式窗口管理在很多情况下能够极大的提升你的工作效率。举个例子,很多时候你都需要打开好几个shell。在gnome底下这就意味着好几个会相互遮挡命令行窗口,大多数时候如果你想要同时看到这些窗口上面的内容就得费神费力的调整它们的位置和大小。而在平铺是窗口下面你完全不需要为这些事情烦心。

另外一个可能会促使你使用平铺的理由就是,用它来调整、切换窗口几乎可以不使用鼠标。这对于硬派的linux狂热者以及痛恨使用笔记本触控板的人来说也是一个不错的选择。

看看下面这个来我桌面的屏幕截图能对awesome有一个直观的印象

如果你是第一次看到Awesome这样的桌面截图一定会觉得手足无措,最常见的窗口边框不见了,你甚至都没法儿关闭或者是最小化一个窗口。事实上在Awesome中是用不着窗口边框的。它默认就提供了9个标签,你大可把不同的窗口分散在这9个标签中而不用去费神调整它们之间的位置,想要切换的时候简单的用快捷件就可以在各个虚拟桌面之间切换。

显然它并不像 gnome,KDE一样有眼花缭乱的桌面特效,但平铺式窗口关注的主要是在其下工作的效率。比如说在写php代码的时候我可以在一个标签中打开四五个shell运行vim同时修改多个文件,然后在另外一个标签中打开一个浏览器。这样可以很快速的输入代码和切换窗口,而你的手甚至不需要离开键盘！

# 为什么是Awesome

平铺式窗口管理器并不是一个新概念,根据维基百科里面的介绍早在施乐公司几十年前最早开始研究图形界面的时候就已经有了平铺的概念了 (尽管和现在我说的不太一样) ,linux下面也有不少成熟的平铺式窗口管理器-Ion, XMonad都是有名的平铺窗口管理器。但是比较之下Awesome有不少鲜明的特点: 

  * 体积小,运行迅速
  
    不过在3.0以后的版本似乎向变得巨大和缓慢的方向发展的趋势
  * 支持多种窗口布局
  
    事实上awesome并不仅仅支持平铺,它还支持好几种各不相同的窗口布局方案
  * 使用Lua脚本的配置文件使得它的灵活性非常高
  
    由于lua脚本的强大能力,Awesome 3.0甚至把窗口管理器运行的逻辑部分放到了配置文件里面。好处是这使得很多其他窗口管理器里面不可能做到的用法在这里变成了可能,坏处是配置文件及其庞大和复杂。
  * 更新迅速
  
    它的作者一直在不停地完善awesome,每次版本升级都能带来一些方便的新特性。不过弊端是造成了awesome的配置文件一直都在改变,说不定什么时候自己以前的配置文件就不能用了
  * 多显示器支持
  
    事实上我一直很羡慕官方网站上awesome运行在多显示器机器上面的截图,口水流了一地。

对于喜欢折腾自己电脑的人来说Awesome是一个很不错的玩具 ![8)][1]不过Awesome所依赖的几个底层库还没有进入稳定版本,而awesome自身也在频繁的更改当中,因此在ubuntu源中的awesome还是老旧的2.4版 (不过ArchLinux源里面倒是有最新版的包) ,想要用awesome的ubuntu用户还是得自己编译才行。编译方法将在以后的文章里面

 [1]: http://static.blogcn.com/wp-includes/images/smilies/icon_cool.gif