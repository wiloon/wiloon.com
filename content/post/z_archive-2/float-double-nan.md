---
title: float double NaN
author: "-"
date: 2015-12-04T05:26:35+00:00
url: /?p=8516
categories:
  - Inbox
tags:
  - reprint
---
## float double NaN

[http://zranye.iteye.com/blog/1344880](http://zranye.iteye.com/blog/1344880)

[http://blog.csdn.net/naruto_ahu/article/details/8805808](http://blog.csdn.net/naruto_ahu/article/details/8805808)

(注意,double中的NaN类似)

在帮助文档中,NaN是这样解释的 (double的,和float差不多) :

<!- Generated by javadoc (build 1.6.0-beta2) on Mon Mar 19 18:22:44 CST 2007 ->

<noscript></noscript>public static final double NaN

保存 double 类型的 NaN 值的常量。它等于 Double.longBitsToDouble(0x7ff8000000000000L) 返回的值。
  
我是这样解释的 (理解不深,有错请不吝赐教) :
  
NaN-Not-a-Number,其实是指一个不合理的实数,在合法却不合理的情况下避免你的程序出现Exception。
  
NaN什么也不是,如果理解成一个超越了float范围的常量 (或者double) ,那也是不对的,为什么？试试判断 (NaN==NaN) 吧,你会发现这返回了一个false。这在java底层是怎么做到的呢？最直接的方式是看java源代码咯。
  
/**
  
* A constant holding a Not-a-Number (NaN) value of type
  
* float.  It is equivalent to the value returned by
  
* Float.intBitsToFloat(0x7fc00000).
  
*/
  
public static final float NaN = 0.0f / 0.0f;
  
上面这一段,就是floa的NaN定义了。这并不是一个超出了范围的常量,而是java的Float中人为得制造出来的一个合法但是不合理的实数了 (看来那位仁兄说得基本都对了) 。所以,NaN==NaN永远等于false啦。
  
那用什么来判断是否合理的Number呢？很奇怪的是,java提供了一个isNaN()的方法,指是否是一个不合理的实数。是否很拗口？我在javascript中第一次接触这个东西,也觉得很奇怪。
  
为什么有这样一个判断的方法不把NaN设置成private的,却又对外开放？难道担心我们不能主动制造一个不合理的实数出来？本人菜鸟,如果有高手能解答,还请不吝赐教,理解未深,有错误请指出,谢谢。
