---
title: user_tables num_rows
author: wiloon
type: post
date: 2015-03-06T02:46:02+00:00
url: /?p=7399
categories:
  - Uncategorized

---
高水位线应该是最高值而不可能是低于700万的值
  
根据英文资料:
  
but it&#8217;s base on your table analysis strategy,may be not accurate
  
select &#8216;analyze table &#8216;||S.TABLE\_NAME||&#8217; compute statistics;&#8217; from  user\_tables s;

翻译如下:
  
但是它是基于你的表分析策略，由于时间的关系，可能已经不准确了。
  
如果你要准确，可以
  
select &#8216;analyze table &#8216;||S.TABLE\_NAME||&#8217; compute statistics;&#8217; from  user\_tables s;
  
后的所有脚本，再运行统计行数。

也就是USER_TABLES中存储的是上一次分析之后的值,而不是准确值.