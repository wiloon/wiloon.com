---
title: Sys.WebForms.PageRequestManagerServerErrorException
author: wiloon
type: post
date: 2013-07-13T06:51:05+00:00
url: /?p=5647
categories:
  - Uncategorized

---
一、
  
Sys.WebForms.PageRequestManagerServerErrorException: An unknown error occurred while processing the request on the server. The status code returned from the server was: <span style="color: #ff0000;">500</span>错误解决办法

转载的解决办法:

A.在Web.Config
  
<system.web>
  
<pages enableEventValidation=&#8221;false&#8221;/>
  
</system.web>

B.在 ScriptManager  内添加 EnablePartialRendering=&#8221;false&#8221; 显示详细的错误信息。
  
如下：
  
<asp:ScriptManager ID=&#8221;ScriptManager1&#8243; runat=&#8221;server&#8221; EnablePartialRendering=&#8221;false&#8221;/>

一般的原因都是页面存在潜在的危险字符  在 页首加入 ValidateRequest=&#8221;false&#8221;
  
如下：
  
<%@ Page Language=&#8221;C#&#8221; MasterPageFile=&#8221;~/MasterPages/CompanyManage.master&#8221; AutoEventWireup=&#8221;true&#8221; CodeFile=&#8221;CompanyIntroEdit.aspx.cs&#8221; Inherits=&#8221;CompanyIntroEdit&#8221; Title=&#8221;Untitled Page&#8221; ValidateRequest=&#8221;false&#8221; %>

C、去掉updatepanel，看是否有代码错误。代码错误改正，再重新添加。

我的解决办法：

google搜的结果前两页，不管中文英文都看过了，依然解决不了。用A方法，不可以。用B方法引起二次错误“Extender controls may not be registered before PreRender” 二次错误，也解决不了。用C方法，取出ajax控件，我的代码依然可以用，没有什么错误。

我的代码出现这个错误，是因为又做了一个接口，最后实在没办法，就把做接口前的代码，拿出来，之后，又把接口的类填上，代码和原来一样，然后就可以了。就这。。。。，很奇怪，想不通，记录下来吧。

&nbsp;

二、
  
Sys.WebForms.PageRequestManagerServerErrorException: An unknown error occurred while processing the request on the server. The status code returned from the server was : <span style="color: #ff0000;">12031<br /> </span>原因是查询出的数据超出web.config中的最大大小（默认4MB）
  
解决办法

在web.config中的<system.web>下增加如下节点，即可解决
  
<httpRuntime maxRequestLength=&#8221;8192&#8243; />
  
ys.WebForms.PageRequestManagerServerErrorException: An unknown error occurred while processing the request on the server. The status code returned from the server was : 12019
  
12019       ERROR\_INTERNET\_INCORRECT\_HANDLE\_STATE
  
The requested operation cannot be carried out because the
  
handle supplied is not in the correct state.

Please refer to this: <a href="http://support.microsoft.com/kb/193625" rel="nofollow">http://support.microsoft.com/kb/193625</a>
  
这里有这个问题的论坛
  
<a href="http://forums.asp.net/p/1126173/2746307.aspx" rel="nofollow">http://forums.asp.net/p/1126173/2746307.aspx</a> <a href="http://forums.asp.net/t/1123365.aspx" rel="nofollow">http://forums.asp.net/t/1123365.aspx</a>
  
三、
  
用IE浏览器查看NetFlow的时候出现 Sys.WebForms.PageRequestManagerServerErrorException: An unknown error occurred while processing the request on the server. The status code returned from the server was: <span style="color: #ff0000;">404</span>错误。这个错误出现是因为 .NET Framework 3.5 SP1 升级产生的。Firefox不会出现错误。

解决方法为打开 InetpubSolarWindsOrionMasterPage.master ，找到
  
<form runat=&#8221;server&#8221; method=&#8221;post&#8221; action=&#8221;#&#8221; id=&#8221;aspnetForm&#8221;>
  
改为 <form runat=&#8221;server&#8221; method=&#8221;post&#8221; id=&#8221;aspnetForm&#8221;> 。