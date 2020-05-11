---
title: XSS, Cross Site Scripting, CSRF, Cross-site request forgery
author: wiloon
type: post
date: 2018-12-13T06:38:16+00:00
url: /?p=13058
categories:
  - Uncategorized

---
跨站脚本攻击(Cross Site Scripting)，缩写为XSS。恶意攻击者往Web页面里插入恶意javaScript代码，当用户浏览该页之时，嵌入其中Web里面的javaScript代码会被执行，从而达到恶意攻击用户的目的。

XSS攻击的分类
  
1、反射型

又称为非持久性跨站点脚本攻击。漏洞产生的原因是攻击者注入的数据反映在响应中。非持久型XSS攻击要求用户访问一个被攻击者篡改后的链接，用户访问该链接时，被植入的攻击脚本被用户游览器执行，从而达到攻击目的。也就是我上面举的那个简单的XSS攻击案例，通过url参数直接注入。然后在响应的数据中包含着危险的代码。

当黑客把这个链接发给你，你就中招啦！

2、存储型

又称为持久型跨站点脚本，它一般发生在XSS攻击向量(一般指XSS攻击代码)存储在网站数据库，当一个页面被用户打开的时候执行。持久的XSS相比非持久性XSS攻击危害性更大,容易造成蠕虫，因为每当用户打开页面，查看内容时脚本将自动执行。

该网页有一个发表评论的功能，该评论会写入后台数据库，并且访问主页的时候，会从数据库中加载出所有的评论。

XSS： 通过客户端脚本语言（最常见如：JavaScript）
  
在一个论坛发帖中发布一段恶意的JavaScript代码就是脚本注入，如果这个代码内容有请求外部服务器，那么就叫做XSS！

CSRF：又称XSRF，冒充用户发起请求（在用户不知情的情况下）,完成一些违背用户意愿的请求（如恶意发帖，删帖，改密码，发邮件等）。
  
XSS更偏向于方法论，CSRF更偏向于一种形式，只要是伪造用户发起的请求，都可成为CSRF攻击。
  
通常来说CSRF是由XSS实现的，所以CSRF时常也被称为XSRF[用XSS的方式实现伪造请求]（但实现的方式绝不止一种，还可以直接通过命令行模式（命令行敲命令来发起请求）直接伪造请求[只要通过合法验证即可]）。
  
XSS更偏向于代码实现（即写一段拥有跨站请求功能的JavaScript脚本注入到一条帖子里，然后有用户访问了这个帖子，这就算是中了XSS攻击了），CSRF更偏向于一个攻击结果，只要发起了冒牌请求那么就算是CSRF了。

https://github.com/OWASP?page=5
  
https://github.com/owasp/java-html-sanitizer
  
https://www.owasp.org/index.php/Category:OWASP\_AntiSamy\_Project
  
https://mvnrepository.com/artifact/org.owasp.antisamy/antisamy
  
https://github.com/GDSSecurity/AntiXSS-for-Java

https://segmentfault.com/a/1190000007059639
  
https://www.freebuf.com/sectool/134015.html
  
https://blog.csdn.net/ru_li/article/details/51334082
  
https://blog.csdn.net/zer0_o/article/details/28399533