---
title: shell params, 参数
author: "-"
date: 2011-08-12T05:32:00+00:00
url: shell-params
categories:
  - shell
tags:
  - reprint
---
## shell params, 参数

在 shell 编程时. 可以使用参数。 Shell 有位置参数和内部参数

### 位置参数

由系统提供的参数称为位置参数。位置参数的值可以用 `$N` 得到, N 是一个数字，如果 为 1， 即 $1. 类似 C 语言中的数组， 
Linux 会把输入的命令字符串分段并给每段进行标号, 标号从 0 开始。第 0 号为程序名字，从 1 开始就表示传递给程序的参数。如 $0 表示程序的名字，$1 表示传递给程序的第一个参数， 以此类推。

### 内部参数

上述过程中的$0是一个内部变量，它是必须的，而$1则可有可无。和$0一样的内部变量还有以下几个。

```Bash
# 传递给程序的总的参数数目
$#

#上一个代码或者 shell 程序在 shell 中退出的情况，如果正常退出则返回 0，反之为非 0 值。
$?
```

$* --传递给程序的所有参数组成的字符串。

下面举例进行说明
  
cat test.sh
  
# !/bin/bash
  
# test shell

```Bash
echo $0
# 第一个参数
echo $1
# 第二个参数
echo $2
# 程序执行结果
echo $?
# 传递参数所组成的字符串
echo $*
# 参数个数
echo $#
```

`./test.sh yema Bhanv edu network`

```Bash
./test.sh //程序名称
Yema //第一个参数
Bhanv //第二个参数
0 //程序执行结果
yema Bhanv edu network //传递参数所组成的字符串
4 //参数个数
```

if [ $1 == " ]; then
  
echo -e 'E[31;40mNo Parameter?'; tput sgr0
  
exit
  
fi

判断相等"==" , 两侧一定要有空格 T_T, 否则会报 "unary operator expected "
