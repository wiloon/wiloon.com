---
title: APK保护技术
author: "-"
date: 2014-03-20T05:21:49+00:00
url: /?p=6408
categories:
  - Inbox
tags:
  - reprint
---
## APK保护技术
http://my.eoe.cn/1177576/archive/10575.html

由于Java字节码的抽象级别较高,因此Android的APK较容易被反编译,而国内对于网络产品保护不力。如果一款应用APK被破解,那么可能会被他人植入广告或者病毒以供他人盈利或窃取用户信息；如果一款游戏APK被破解,那么这款游戏可能会从收费版变成免费版,游戏的支付系统也形同虚设。不管是哪种情况,对于开发者来说,APK被破解绝对是一场噩梦,而自己手动设置各种加密不但耗时耗力,而且不一定能收到很好的效果。本文介绍几种常用的方法,用于保护Java字节码不被反编译。通常,这些方法不能够绝对防止程序被反编译,而是加大反编译的难度而已,因为这些方法都有自己的使用环境和弱点。


1．隔离Java程序



最简单的方法就是让用户不能够访问到Java Class程序,这种方法是最根本的方法,具体实现有多种方式。例如,开发人员可以将关键的Java Class放在服务器端,客户端通过访问服务器的相关接口来获得服务,而不是直接访问Class文件。这样黑客就没有办法反编译Class文件。目前,通过接口提供服务的标准和协议也越来越多,例如 HTTP、Web Service、RPC等。但是有很多应用都不适合这种保护方式,例如对于单机运行的程序就无法隔离Java程序。

2．对Class文件进行加密 



2

为了防止Class文件被直接反编译,许多开发人员将一些关键的Class文件进行加密,例如对注册码、序列号管理相关的类等。在使用这些被加密的类之前,程序首先需要对这些类进行解密,而后再将这些类装载到JVM当中。这些类的解密可以由硬件完成,也可以使用软件完成。 

在实现时,开发人员往往通过自定义ClassLoader类来完成加密类的装载(注意由于安全性的原因,Applet不能够支持自定义的ClassLoader)。自定义的ClassLoader首先找到加密的类,而后进行解密,最后将解密后的类装载到JVM当中。在这种保护方式中,自定义的ClassLoader是非常关键的类。由于它本身不是被加密的,因此它可能成为黑客最先攻击的目标。如果相关的解密密钥和算法被攻克,那么被加密的类也很容易被解密。

3．转换成本地代码 



将程序转换成本地代码也是一种防止反编译的有效方法。因为本地代码往往难以被反编译。开发人员可以选择将整个应用程序转换成本地代码,也可以选择关键模块转换。如果仅仅转换关键部分模块,Java程序在使用这些模块时,需要使用JNI技术进行调用。当然,在使用这种技术保护Java程序的同时,也牺牲了Java的跨平台特性。对于不同的平台,我们需要维护不同版本的本地代码,这将加重软件支持和维护的工作。不过对于一些关键的模块,有时这种方案往往是必要的。为了保证这些本地代码不被修改和替代,通常需要对这些代码进行数字签名。在使用这些本地代码之前,往往需要对这些本地代码进行认证,确保这些代码没有被黑客更改。如果签名检查通过,则调用相关JNI方法。

4．代码混淆



代码混淆是对Class文件进行重新组织和处理,使得处理后的代码与处理前代码完成相同的功能(语义)。但是混淆后的代码很难被反编译,即反编译后得出的代码是非常难懂、晦涩的,因此反编译人员很难得出程序的真正语义。从理论上来说,黑客如果有足够的时间,被混淆的代码仍然可能被破解,甚至目前有些人正在研制反混淆的工具。但是从实际情况来看,由于混淆技术的多元化发展,混淆理论的成熟,经过混淆的Java代码还是能够很好地防止反编译。下面我们会详细介绍混淆技术,因为混淆是一种保护Java程序的重要技术。

5.在线加密



APK Protect(http://www.apkprotect.com/)是一个在线对APK程序进行加密的网站,可以支持Java和C+ +语言的保护,能达到反调试、反编译的效果,操作过程简单易用: 仅需上传你的APK,选择加密项目,等待服务器加密 (通常一两个小时左右) 后即可下载加壳的APK,然后再签名上传到应用市场即可。经测试,通过APK Protect加密的APK变得非常难以破解,从而保护了APK。

from eoe.cn