---
title: 修改 MySQL 字符集
author: "-"
date: 2011-04-16T12:35:48+00:00
url: /?p=69
categories:
  - DataBase
tags:
  - MySQL

---
## 修改 MySQL 字符集
查看字符集设置

```bash
   
show variables like 'character_set_%';
  
```
  
```bash
  
set character_set_client=utf8;
  
set character_set_connection=utf8;
  
set character_set_database=utf8;
  
set character_set_results=utf8;
  
set character_set_server=utf8;
  
```

set character_set_database=utf8;
  
..character_set_client
  
..character_set_connection
  
..character_set_database

sudo emacs /etc/MySQL/my.cnf
  
在[client]下添加
  
default-character-set=utf8

在[MySQLd]下添加
  
default-character-set=utf8 (!!!MySQL 5.5.20 以后的版本要这样改 character-set-server=utf8)

```sql
  
- 查看表编码
  
SHOW CREATE TABLE tbl_name;
  
```

改表的字符集.
  
ALTER TABLE tbl_name CONVERT TO CHARACTER SET utf8;

重启MySQL;
  
sudo /etc/init.d/MySQL restart;


  
    MySQL修改表、字段、库的字符集
  


http://fatkun.com/2011/05/MySQL-alter-charset.html/embed#?secret=tmPf4RUxzk
  
http://www.unix.com/red-hat/168071-MySQL-MySQLd-fails-start.html