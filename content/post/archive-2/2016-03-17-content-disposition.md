---
title: Content-disposition
author: wiloon
type: post
date: 2016-03-17T05:13:16+00:00
url: /?p=8803
categories:
  - Uncategorized

---
Content-disposition 是 MIME 协议的扩展，MIME 协议指示 MIME 用户代理如何显示附加的文件。当 Internet Explorer 接收到头时，它会激活**文件下载**对话框，它的文件名框自动填充了头中指定的文件名。（请注意，这是设计导致的；无法使用此功能将文档保存到用户的计算机上，而不向用户询问保存位置。）

服务端向客户端游览器发送文件时，如果是浏览器支持的文件类型，一般会默认使用浏览器打开，比如txt、jpg等，会直接在浏览器中显示，如果需要提示用户保存，就要利用Content-Disposition进行一下处理，关键在于一定要加上attachment：

Response.AppendHeader(&#8220;Content-Disposition&#8221;,&#8221;attachment;filename=FileName.txt&#8221;);

备注：这样浏览器会提示保存还是打开，即使选择打开，也会使用相关联的程序比如记事本打开，而不是IE直接打开了。

Content-Disposition就是当用户想把请求所得的内容存为一个文件的时候提供一个默认的文件名。具体的定义如下：

content-disposition = “Content-Disposition” “:”

disposition-type *( “;” disposition-parm )

disposition-type = “attachment” | disp-extension-token

disposition-parm = filename-parm | disp-extension-parm

filename-parm = “filename” “=” quoted-string

disp-extension-token = token

disp-extension-parm = token “=” ( token | quoted-string )

那么由上可知具体的例子：

Content-Disposition: attachment; filename=“filename.xls”

当然filename参数可以包含路径信息，但User-Agnet会忽略掉这些信息，只会把路径信息的最后一部分做为文件名。当你在响应类型为application/octet- stream情况下使用了这个头信息的话，那就意味着你不想直接显示内容，而是弹出一个”文件下载”的对话框，接下来就是由你来决定“打开”还是“保存” 了。

注意事项：

1.当代码里面使用Content-Disposition来确保浏览器弹出下载对话框的时候。 response.addHeader(&#8220;Content-Disposition&#8221;,&#8221;attachment&#8221;);一定要确保没有做过关于禁止浏览器缓存的操作。如下：

response.setHeader(&#8220;Pragma&#8221;, &#8220;No-cache&#8221;);
  
response.setHeader(&#8220;Cache-Control&#8221;, &#8220;No-cache&#8221;);
  
response.setDateHeader(&#8220;Expires&#8221;, 0);

不然会发现下载功能在**opera**和**firefox**里面好好的没问题，在IE下面就是不行，就是找不到文件。