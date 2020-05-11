---
title: linux tool
author: wiloon
type: post
date: 2018-10-09T03:10:52+00:00
url: /?p=12772
categories:
  - Uncategorized

---
https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/ldd.html

ldd
  
作用：用来查看程式运行所需的共享库,常用来解决程式因缺少某个库文件而不能运行的一些问题。
  
示例：查看test程序运行所依赖的库:

/opt/app/todeav1/test$ldd test
  
libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x00000039a7e00000)
  
libm.so.6 => /lib64/libm.so.6 (0x0000003996400000)
  
libgcc\_s.so.1 => /lib64/libgcc\_s.so.1 (0x00000039a5600000)
  
libc.so.6 => /lib64/libc.so.6 (0x0000003995800000)
  
/lib64/ld-linux-x86-64.so.2 (0x0000003995400000)