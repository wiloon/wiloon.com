---
title: Apache Shiro
author: "-"
date: 2012-12-12T04:49:01+00:00
url: shiro
categories:
  - Java
tags:
  - reprint
---
## Apache Shiro

Apache Shiro (发音为"shee-roh",日语"堡垒 (Castle) "的意思) 是一个强大易用的Java安全框架,提供了认证、授权、加密和会话管理功能,可为任何应用提供安全保障 - 从命令行应用、移动应用到大型网络及企业应用。

Shiro为解决下列问题 (我喜欢称它们为应用安全的四要素) 提供了保护应用的API:

* 认证 - 用户身份识别,常被称为用户"登录"；
* 授权 - 访问控制；
* 密码加密 - 保护或隐藏数据防止被偷窥；
* 会话管理 - 每用户相关的时间敏感的状态。

Shiro还支持一些辅助特性,如Web应用安全、单元测试和多线程,它们的存在强化了上面提到的四个要素。

### Subject

在考虑应用安全时,你最常问的问题可能是"当前用户是谁？"或"当前用户允许做X吗？"。当我们写代码或设计用户界面时,问自己这些问题很平常: 应用通常都是基于用户故事构建的,并且你希望功能描述 (和安全) 是基于每个用户的。所以,对于我们而言,考虑应用安全的最自然方式就是基于当前用户。Shiro的API用它的Subject概念从根本上体现了这种思考方式。

**Subject**一词是一个安全术语,其基本意思是"当前的操作用户"。称之为"用户"并不准确,因为"用户"一词通常跟人相关。在安全领域,术语"Subject"可以是人,也可以是第三方进程、后台帐户 (Daemon Account) 或其他类似事物。它仅仅意味着"当前跟软件交互的东西"。但考虑到大多数目的和用途,你可以把它认为是Shiro的"用户"概念。在代码的任何地方,你都能轻易的获得Shiro Subject,

[http://www.infoq.com/cn/articles/apache-shiro](http://www.infoq.com/cn/articles/apache-shiro)

[http://kdboy.iteye.com/blog/1154644](http://kdboy.iteye.com/blog/1154644)
