---
title: cygwin 默认英文环境
author: "-"
date: 2016-01-14T05:46:02+00:00
url: /?p=8669
categories:
  - Inbox
tags:
  - reprint
---
## cygwin 默认英文环境

搜索结果里充斥着大量让cygwin显示中文的文章以及各种版本转载,我不知道转载这些文章的同学到底自己亲自试验过没有,显示出来的中文往往只显示一半,这样也能算能显示中文了？这么多坑爹的结果,却没有一个告诉大家怎么使用英文界面的方法？

目前的cygwin的shell界面往往是由windows的localization设定决定的,所以如果你将local设定为中国,那么界面默 认都是已经能显示中文的了。但cygwin的中文显示,不但老是出问题,而且也不好看。所以将其界面直接改变成英文,倒也是一个不错的选择之一,只不过这 里提醒大家,你的目录或者文件有很多中文名的,那么最好还是显示中文的比较好,虽然可以让英文shell显示中文,但是显示还是不好看。

让cygwin shell使用英文界面只用在~/.bashrc这个文件里面加上一句话:

export LANG='en_US'
  
如果想使用其他界面,把en_US改成其他语言对应的地区代码就行了,比如说zh_CN。其实LANG的"完全体"是"地区.编码"这样的结构。所以如果要想自己设置编码,还需要在zh_CN的后面加上编码——这里用GBK来说明——使其变成zh_CN.GBK。在这里我们要让界面是英文同时能让中文也能显示,所以将LANG设置成en_US.GBK。

到此界面语言修改完成,简单吧？事情没那么简单,在cygwin下面,只做这样的设置,会让vi等软件出现匪夷所思的现象,难道是……打开方式不 对？No,其实语言的设置还没完。执行locale命令,会返回给你目前所有语言设置,可以说有关语言设置的项目还是挺丰富的嘛,LC下还有许多要设置 的,不过LC下面有一个LC_ALL,看样子设置他就行了,.bashrc加上一句:

export LC_ALL='en_US.GBK'
  
Bingo！重启cygwin之后,vi恢复正常行为。

转载自 [http://hi.baidu.com/chrisyue/blog/item/41225fe79cedb33db838207a](http://hi.baidu.com/chrisyue/blog/item/41225fe79cedb33db838207a) .html
