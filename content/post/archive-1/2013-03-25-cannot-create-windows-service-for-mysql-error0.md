---
title: Cannot create Windows Service for MySQL.Error:0
author: wiloon
type: post
date: 2013-03-25T04:50:29+00:00
url: /?p=5345
categories:
  - DataBase
tags:
  - MySQL

---
http://qqhugo.blogbus.com/logs/15733455.html
  
安装新的MYSLQ数据库，安装好运行MySQL Server Instance ConfigWizard，在最后一步却发现无法启动服务，出现这样的提示“cannotcreate windows service for mysql.error:0”！
  
找了很久终于搞到一点资料，解决方法如下：
  
原因：
  
安装mysql时可能产生cannot create windows service formysql.error:0错误,错误的原因多数由于重新安装mysql或者对mydql升级,使用MySQLConfiguration Wizard而产生.
  
解决方法：
  
可以使用mysql以外的服务名,比如mysql11,等.但这不是最好的解决方法,我们可以使用windows的sc程序删除mysql服务.
  
C:>sc delete mysql
  
[SC] DeleteService SUCCESS
  
再重新使用MySQL Configuration Wizard,就不会有此错误了。
  
如果还是不行,重新启动一下电脑就OK了