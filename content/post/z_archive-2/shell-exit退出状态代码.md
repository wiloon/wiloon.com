---
title: shell 状态代码
author: "-"
date: 2019-01-07T06:03:34+00:00
url: /?p=13349
categories:
  - shell
tags:
  - reprint
---
## shell 状态代码

[https://blog.csdn.net/hongkangwl/article/details/16184883](https://blog.csdn.net/hongkangwl/article/details/16184883)

Linux提供 `$?` 特殊变量来保存最后一条命令执行结束的退出状态。执行完一条命令后,立即执行 echo $?, 可以查看最后一条命令的退出状态值。

正常的情况下,命令成功执行完成的退出状态是0,如果非0,则命令执行有错。
  
该命令可以用于检查命令是否正确执行,比如在解压包的时候,检查解压包是否成功十分有效。

自定义退出状态码,可以在脚本中定义自己的退出状态代码,然后使用 echo $? 检查。

退出状态码最高是255,一般自定义的代码值为0~255,如果超出255,则返回该数值被256除了之后的余数。

### shell 退出状态代码

- 0 命令成功完成
- 1 通常的未知错误
- 2 误用 shell 命令
- 126 命令无法执行
- 127 没有找到命令
- 128 无效的退出参数
- 128+x 使用Linux信号x的致命错误。
- 130 使用Ctrl-C终止的命令
- 255 规范外的退出状态

作者: 王伴农
来源: CSDN
原文: [https://blog.csdn.net/hongkangwl/article/details/16184883](https://blog.csdn.net/hongkangwl/article/details/16184883)
版权声明: 本文为博主原创文章,转载请附上博文链接！
