---
title: excel vlookup
author: "-"
date: 2014-03-21T05:37:34+00:00
url: /?p=6420
categories:
  - Inbox
tags:
  - Excel

---
## excel vlookup
http://www.utosee.com/post/vlookup.html 

今天在百度知道的时候，看到旁边有人问excel中条件查找vlookup的问题，有几位高手都知道使用vlookup作答，可惜都是没有经过测试，直接复制别人的答案，让所有的读者都无法实施，一头雾水。今天我们详细解答一下vlookup函数的实际应用问题: 


问题: 如下图，已知表sheet1中的数据如下，如何在数据表二 sheet2 中如下引用: 当A列学号随机出现的时候，如何在B列显示其对应的物理成绩？


vlookup-excel数据引用


首先我们知道需要用到vlookup函数，那么先介绍一下使用 vlookup函数的几个参数，vlookup是判断引用数据的函数，它总共有四个参数，依次是: 


1. 判断的条件

2. 跟踪数据的区域

3. 返回第几列的数据

4. 是否精确匹配


根据以上参考，和上述在sheet2表的B列显示问题的实际需求，在sheet2表的B2单元格输入这个公式是: 


=vlookup(a2,sheet1!$a$2:$f$100,6,true)


详细说明一下，在此vlookup函数例子中各个参数的使用说明: 


1. a2 是判断的条件，也就是说sheet1表和sheet2表中学号相同者，即sheet2表a列对应的数据和sheet1表中学号列a列的数据相同方能引用；

2. sheet1!$a$2:$f$100 是数据跟踪的区域，因为需要引用的数据在f列，所以跟踪的区域至少在f列，sheet1!是不同表间引用所用的表名称，和标志是表间引用的!符号，$是绝对引用 (关于绝对引用可以参考这里) ，$a$2:$f$100 表明从A2到F100单元格的数据区域，如果数据区域不止100，那么可以直接使用A:F，这样虽然方便但是有风险，因为如果sheet1表的下方还有其它数据，就有可能出现问题；

3. 6 这是返回什么数的列数，如上图的物理是第6列，所以应该是6，如果要求英语的数值，那么此处应该是5

4. 是否绝对引用，如果是就输入 true 如果是近似即可满足条件 那么输入false  (近似值主要用于带小数点的财务、运算等) 

5. vlookup是垂直方向的判断，如果是水平方向的判断可使用Hlookup函数


结果如下图: 


vlookup-excel数据引用


不知道你是否已经会使用vlookup这个条件查找函数，如果你有兴趣可以试试本例。与本例结合紧密的是excel数据引用，更多的excel可以参考这里。


本文是 有图小站 原创，原地址 http://www.utosee.com/post/vlookup.html 转载请保留。