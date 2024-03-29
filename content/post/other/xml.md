---
title: XML
author: "-"
date: 2012-06-10T04:36:08+00:00
url: /?p=3471
categories:
  - Development
tags:
  - reprint
---
## XML
XML即可扩展标记语言 (eXtensible Markup Language) ，是被设计用来描述、存储、传送及交换数据。XML是当前处理结构化文档信息的有力工具，标记是指计算机所能理解的信息符号
  
    XML是什么？XML即可扩展标记语言 (eXtensible Markup Language) ，是被设计用来描述、存储、传送及交换数据。XML是当前处理结构化文档信息的有力工具，标记是指计算机所能理解的信息符号，通过此种标记，计算机之间可以处理包含各种信息的文章等。简单说，XML就是一种数据的描述语言，你可以把它理解为微型数据库，对了！它就是数据库。
  
  
    作为新手的你，可能还无法理解它，没有关系，理论都是死的，不妨从应用中了解它！如果你是一个开发人员或准备成为一个开发人员，必然会有恍然大悟的一刻。
  
  
    下面了解一下XML版本
  
  
    1998年2月10日，XML 1.0作为一项 W3C推荐被发布。
  
  
    2000年10月6日，XML 1.0(SE)作为一项 W3C 推荐被发布。第二版仅仅是在合并第一版的勘误表的基础上进行的修正 (漏洞修复) 。
  
  
    2001年12月13日，XML 1.1作为一份工作草案被发布，并作为一项候选推荐发布于2002年10月15日。XML 1.1 允许在名称中使用几乎所有的 Unicode 字符。
  
  
  
  
    
      
        
          可扩展标记语言 (Extensible Markup Language, XML) ，用于标记电子文件使其具有结构性的标记语言，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。
        
      
      
      
      
  
  
  
    XML与Access,Oracle和SQL Server等数据库不同，数据库提供了更强有力的数据存储和分析能力，例如: 数据索引、排序、查找、相关一致性等，XML仅仅是存储数据。事实上XML与其他数据表现形式最大的不同是: 他极其简单。这是一个看上去有点琐细的优点，但正是这点使XML与众不同。 
    
    
    
    
    
      XML与HTML的设计区别是: XML是用来存储数据的，其焦点是数据的内容。而HTML 被设计用来显示数据，其焦点是数据的外观。
    
    
    
    
    
    
      XML和HTML语法区别: HTML的标记不是所有的都需要成对出现，XML则要求所有的标记必须成对出现；HTML标记不区分大小写，XML则相反。
    
    
    
      结合
    
    
    
      XML的简单使其易于在任何应用程序中读写数据，这使XML很快成为数据交换的唯一公共语言，虽然不同的应用软件也支持其它的数据交换格式，但不久之后他们都将支持XML，那就意味着程序可以更容易的与Windows,Mac OS,Linux以及其他平台下产生的信息结合，然后可以很容易加载XML数据到程序中并分析他，并以XML格式输出结果。
    
    
    
      友好
    
    
    
      为了使得SGML显得用户友好，XML重新定义了SGML的一些内部值和参数，去掉了大量的很少用到的功能，这些繁杂的功能使得SGML在设计网站时显得复杂化。XML保留了SGML的结构化功能，这样就使得网站设计者可以定义自己的文档类型，XML同时也推出一种新型文档类型，使得开发者也可以不必定义文档类型。
    
    
    
    
    
    
      简明语法
    
    
    
      SGML常用来定义针对HTML的文档类型定义 (DTD) ，同时它也常用于编写XML的DTD。SGML的问题就在于，它允许出现一些奇怪的语法，这让创建HTML的解析器成为一个大难题: 
    
    
    
    
    
    
      1 某些起始标签不允许出现结束标签，例如HTML中<img>标签。包含了结束标签就会出现错误。
    
    
    
    
    
    
      2 某些起始标签可以选择性出现结束标签或者隐含了结束标签。
    
    
    
    
    
    
      3 某些起始标签要求必须出现结束标签，例如HTML中<script>标签。
    
    
    
    
    
    
      4 标签可以以任何顺序嵌套。即使结束标签不按照起始标签的逆序出现也是允许的，例如，This is a sample string是正确的。
    
    
    
    
    
    
      5 某些特性要求必须包含值，例如<img src="picture.jpg">中的src特性。
    
    
    
    
    
    
      6 某些特性不要求一定有值，例如中的nowrap特性。
    
    
    
    
    
    
      7 定义特性的两边有没有加上双引号都是可以的，所以<img src="picture.jpg">和<img src=picture.jpg>都是允许的。
    
    
    
    
    
    
      这些问题使建立一个SGML语言的解析器变成了一项艰巨的任务。判断何时应用以上规则的困难导致了SGML语言的定义一直停滞不前。以这些问题作为出发点，XML逐渐步入我们的视野。
    
    
    
    
    
    
      XML去掉了之前令许多开发人员头疼的SGML的随意语法。在XML中，采用了如下的语法: 
    
    
    
    
    
    
      1 任何的起始标签都必须有一个结束标签。
    
    
    
    
    
    
      2 可以采用另一种简化语法，可以在一个标签中同时表示起始和结束标签。这种语法是在大于符号之前紧跟一个斜线 (/) ，例如<tag/ >。XML解析器会将其翻译成<tag></tag>。
    
    
    
    
    
    
      3 标签必须按合适的顺序进行嵌套，所以结束标签必须按镜像顺序匹配起始标签，例如this is a samplestring。这好比是将起始和结束标签看作是数学中的左右括号: 在没有关闭所有的内部括号之前，是不能关闭外面的括号的。
    
    
    
    
    
    
      4 所有的特性都必须有值。
    
    
    
    
    
    
      5 所有的特性都必须在值的周围加上双引号。
    
    
    
    
    
    
      这些规则使得开发一个XML解析器要简便得多，而且也除去了解析SGML中花在判断何时何地应用那些奇怪语法规则上的工作。仅仅在XML出现后的前六年就衍生出多种不同的语言，包括MathML、SVG、RDF、RSS、SOAP、XSLT、XSL-FO，而同时也将HTML改进为XHTML。
  


### XHTML Validator
 
http://validator.w3.org/

