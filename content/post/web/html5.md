---
title: HTML5
author: "-"
date: 2011-11-20T15:12:57+00:00
url: /?p=1561
categories:
  - Web
tags:
  - HTML

---
## HTML5
<!DOCTYPE html>
  
<html>
  
<head>
  
<!-css->
  

  
<script src="script.js"></script>

<script type="text/javascript">
  
var myVar="hello";
  
function showAlert() { alert('You triggered an alert!'); }
  
</script>
  
</head>
  
<body>
  
Hello html
  
</body>
  
</html>

文档申明: Doctype: <!DOCTYPE html>

css:可以不写type="text/css"了，浏览器只要看到rel="stylesheet"就知道你是链接样式表了。



javascript:可以不写 type="text/javascript"，浏览器默认的脚本语言就是javascript。
  
<script src="script.js"></script>

可以简单的把所有内容都包装在一个链接元素中

  Headline text
 Paragraph text.

可以改写为: 

  
 Headline text
 Paragraph text.
 

HTML5支持已有的内容，原来的div,img,p不做任何改动，都可以在HTML5里正常使用。

原结构: 

  <body>
 ...
 ...
 ...
 ...
 ...
 </body>

可以更改为: 

  <body>
 ...
 <nav>...</nav>
 <section>...</section>
 ...</aside>
 <footer>...</footer>
 </body>


