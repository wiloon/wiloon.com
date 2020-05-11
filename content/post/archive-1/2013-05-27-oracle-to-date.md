---
title: oracle to date
author: wiloon
type: post
date: 2013-05-27T09:06:31+00:00
url: /?p=5487
categories:
  - DataBase

---
在[Oracle][1]数据库中，Oracle to\_date()函数是我们经常使用的函数，下面就为您详细介绍Oracle to\_date()函数的用法，希望可以对您有所启迪。

to_date()与24小时制表示法及mm分钟的显示：

一、在使用Oracle的to_date函数来做日期转换时，很多Java程序员也许会直接的采用“yyyy-MM-dd HH:mm:ss”的格式作为格式进行转换，但是在Oracle中会引起错误：“ORA 01810 格式代码出现两次”。

  1. select to_date(&#8216;2005-01-01 13:14:20&#8242;,&#8217;yyyy-MM-dd HH24:mm:ss&#8217;) from dual;

如：
  
原因是SQL中不区分大小写，MM和mm被认为是相同的格式代码，所以Oracle的SQL采用了mi代替分钟。

  1. select to_date(&#8216;2005-01-01 13:14:20&#8242;,&#8217;yyyy-MM-dd HH24:mi:ss&#8217;) from dual;

二、另要以24小时的形式显示出来要用HH24
  
select to_char(sysdate,&#8217;yyyy-MM-dd HH24:mi:ss&#8217;) from dual;//mi是分钟
  
select to\_char(sysdate,&#8217;yyyy-MM-dd HH24:mm:ss&#8217;) from dual;//mm会显示月份  oracle中的to\_date参数含义

1.日期格式参数 含义说明
  
D 一周中的星期几
  
DAY 天的名字，使用空格填充到9个字符
  
DD 月中的第几天
  
DDD 年中的第几天
  
DY 天的简写名
  
IW ISO标准的年中的第几周
  
IYYY ISO标准的四位年份
  
YYYY 四位年份
  
YYY,YY,Y 年份的最后三位，两位，一位
  
HH 小时，按12小时计
  
HH24 小时，按24小时计
  
MI 分
  
SS 秒
  
MM 月
  
Mon 月份的简写
  
Month 月份的全名
  
W 该月的第几个星期
  
WW 年中的第几个星期  1.日期时间间隔操作
  
当前时间减去7分钟的时间
  
select sysdate,sysdate &#8211; interval ’7’ MINUTE from dual
  
当前时间减去7小时的时间
  
select sysdate &#8211; interval ’7’ hour from dual
  
当前时间减去7天的时间
  
select sysdate &#8211; interval ’7’ day from dual
  
当前时间减去7月的时间
  
select sysdate,sysdate &#8211; interval ’7’ month from dual
  
当前时间减去7年的时间
  
select sysdate,sysdate &#8211; interval ’7’ year from dual
  
时间间隔乘以一个数字
  
select sysdate,sysdate &#8211; 8 *interval ’2’ hour from dual

2.日期到字符操作

  1. select sysdate,to_char(sysdate,’yyyy-mm-dd hh24:mi:ss’) from dual
  2. select sysdate,to_char(sysdate,’yyyy-mm-dd hh:mi:ss’) from dual
  3. select sysdate,to_char(sysdate,’yyyy-ddd hh:mi:ss’) from dual
  4. select sysdate,to_char(sysdate,’yyyy-mm iw-d hh:mi:ss’) from dual

参考oracle的相关关文档(ORACLE901DOC/SERVER.901/A90125/SQL_ELEMENTS4.HTM#48515)

3. 字符到日期操作

  1. select to_date(’2003-10-17 21:15:37’,’yyyy-mm-dd hh24:mi:ss’) from dual

具体用法和上面的to_char差不多。

4. trunk/ ROUND函数的使用

  1. select trunc(sysdate ,’YEAR’) from dual
  2. select trunc(sysdate ) from dual
  3. select to_char(trunc(sysdate ,’YYYY’),’YYYY’) from dual

5.oracle有毫秒级的数据类型
  
&#8211;返回当前时间 年月日小时分秒毫秒
  
select to\_char(current\_timestamp(5),’DD-MON-YYYY HH24:MI:SSxFF’) from dual;
  
&#8211;返回当前 时间的秒毫秒，可以指定秒后面的精度(最大=9)
  
select to\_char(current\_timestamp(9),’MI:SSxFF’) from dual;

6.计算程序运行的时间(ms)

  1. declare
  2. type rc is ref cursor;
  3. l_rc rc;
  4. l\_dummy all\_objects.object_name%type;
  5. l\_start number default dbms\_utility.get_time;
  6. begin
  7. for I in 1 .. 1000
  8. loop
  9. open l_rc for
 10. ’select object\_name from all\_objects ’||
 11. ’where object_id = ’ || i;
 12. fetch l\_rc into l\_dummy;
 13. close l_rc;
 14. end loop;
 15. dbms\_output.put\_line
 16. ( round( (dbms\_utility.get\_time-l_start)/100, 2 ) ||
 17. ’ seconds&#8230;’ );
 18. end;

 [1]: http://database.51cto.com/art/201009/228094.htm