---
title: favicon
author: "-"
date: 2011-12-17T08:26:27+00:00
url: /?p=1918
categories:
  - Web
tags:
  - reprint
---
## favicon

  
    
      
        所谓favicon，即Favorites Icon的缩写，顾名思义，便是其可以让浏览器的收藏夹中除显示相应的标题外，还以图标的方式区别不同的网站。当然，这不仅仅是Favicon的全部，根据浏览器的不同，Favicon显示也有所区别: 在大多数主流浏览器如FireFox和Internet Explorer (5.5及以上版本)中，favicon不仅在收藏夹中显示，还会同时出现在地址栏上，这时用户可以拖曳favicon到桌面以建立到网站的快捷方式；除此之外，标签式浏览器甚至还有不少扩展的功能，如FireFox甚至支持动画格式的favicon等。
      
  


  在我们的网站建设中，为网站打造一个契合网站主题的个性化标志则是必需的，这直接关系到能否成功地塑造网站的品牌。这从某些角度看仍在网站推广的范畴之内，而欲取得成功，不仅包括良好的页面设计、令人印象深刻的网站Logo，也包括favicon。favicon显示位置,从特定的技术角度看，favicon也并不只是仅仅让网站给人更专业的观感，也可以在一定程度上减轻服务器的流量带宽占用: 一般为了提高网站的可用性，我们都会为自己的网站创建一个自定义的404错误文件，在这种情况下，如果网站没有相应的favicon.ico文件，每当有用户收藏网站/网页时， Web服务器都会调用这个自定义的404文件，并在网站的错误日志中记录。这显然是应该予以避免的。 
  
  
  
    如何制作Favicon.ico
  
  
    制作Favicon.ico的方法相当简单，首先，利用图形工具创建2个反映网站主题的256色的小图片: 1个为32×32像素，另一个为16 ×16像素。需要注意的是，调色板要选用"Windows 默认调色板"，不然，在最终的效果展示中图形可能会发生迥异于您初衷的颜色上变化。
  
  
  
    需要说明的是，在很多关于Favicon.ico的说明中，常见到要求图片为16色的说法，应该说这类说法大大过时: 在早期如Windows 95时期，16色的Favicon.ico可能是个稳妥的选择，保证其在大多数情况下正常使用，但现在，完全不存在那类限制，16色只能使图标的展示效果大大降低。
  
  
  
    至于在浏览器中使用时16×16像素的图片已经足够，为什么还要准备32×32像素的图片，原因在于，正如上文所言，favicon也显示在地址栏中，用户可以拖曳favicon到桌面以建立到网站的快捷方式，而桌面图标则要以32×32显示的，如果您的Favicon.ico不包括32像素的图片，系统就只能使用默认的浏览器图标来标注网站/网页，如Internet Explorer的蓝色"e"，起不到我们意欲通过Favicon.ico打造网站品牌的作用。
  
  
  
    图片制作好后，使用如Image2Ico之类的小程序即可将2张图片转换到一个Icon文件中
  
  
  
    在网页中使用Favicon.ico
  
  
  
    浏览器调用Favicon的原则是首先在网页所在的目录下寻找Favicon.ico文件，如果没有，便到网站的根目录下寻找。
  
  
  
    因此，在网页中使用Favicon最简单的办法便是将制作好的图标文件命名为Favicon.ico，然后将其上传到网站的根目录即可。
  
  
  
    如果您需要将Favicon.ico放到其他目录下，或者希望让不同的网页显示不同的Favicon，就需要在网页Html文件中做设定了，具体设置也很简单，在Html中的<head>部分加入如下的代码: 
  
  
  
    程序代码: 
  
  
  
    
  
