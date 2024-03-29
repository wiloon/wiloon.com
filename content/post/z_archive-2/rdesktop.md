---
title: 远程桌面, freerdp, rdesktop
author: "-"
date: 2016-10-18T08:23:01+00:00
url: rdesktop
categories:
  - Desktop
tags:
  - reprint

---
## 远程桌面, freerdp, rdesktop

## freerdp

```bash
pacman -S freerdp
xfreerdp /u:"user0" /v:192.168.50.182:3389
xfreerdp /v:192.168.50.9:3389 /u:"wiloon" /p:password0 /size:1680x1024  +clipboard
xfreerdp /v:192.168.50.6:3389 /u:"wiloon" /p:password0 /size:1680x1024  +clipboard
```

## freerdp-shadow-cli

```bash
freerdp-shadow-cli -auth /port:12345
```

[https://www.mankier.com/1/freerdp-shadow-cli#](https://www.mankier.com/1/freerdp-shadow-cli#)

## windows 远程桌面

### 剪切板

[http://www.cnblogs.com/hobinly/archive/2012/09/20/2694708.htmlT2001V00000087FG3](http://www.cnblogs.com/hobinly/archive/2012/09/20/2694708.htmlT2001V00000087FG3)

这几天遇到远程桌面控制服务器时, 无法剪切、粘贴一些东西, 上网搜了一下, 原来是 rdpclip.exe(remote desktop clipboard)不起作用了。此程序负责管理本地机与远程服务器之间共享剪切板,它运行在远程服务器上。

解决方法:

1. 打开任务管理器－〉结束此进程 (rdpclip.exe)

2. 点击任务管理器菜单"文件－〉新建任务",在输入框中输入"rdpclip",确定

### linux连接到windows远程桌面

#### windows设置

系统属性>远程桌面
  
选择 "允许远程连接到此计算机", 取消选择 "公允许运行使用网络级别身份验证的远程桌面计算机连接"

## linux rdesktop

```bash
rdesktop 192.168.50.104 -g 1400x900 -u '<username>' -p <password> -r clipboard:CLIPBOARD

# 非3389端口
rdesktop 192.168.50.104:3390 -g 1400x900 -u '<username>' -p <password> -r clipboard:CLIPBOARD

# 域控用户
rdesktop 192.168.50.104 -g 1400x900 -u '<domain>\<username>' -p <password> -r clipboard:CLIPBOARD
rdesktop 192.168.50.104 -g 1400x900 -u 'AD\<username>' -p <password> -r clipboard:CLIPBOARD
```

```bash
rdesktop -g 1024x768 -d 24 hostname -u user0 -p password0
# PRIMARYCLIPBOARD 不稳定，用CLIPBOARD
# rdesktop -g 1020x685 -d 24 192.168.1.199 -r clipboard:PRIMARYCLIPBOARD
rdesktop -g 1020x685 -d 24 192.168.1.199 -r clipboard:CLIPBOARD

# -M 使用本地鼠标
```

rdesktop 是UNIX 和Linux 系统的一个远程桌面连接软件，它通过MicrosoftWindows NT、Windows 2000 提供的终端服务(Terminal Services)以及WindowsXP 的远程桌面服务(Remote Desktop)，能在Linux系统下远程登录Windows的窗口系统并使用。

一、 **rdesktop的下载和安装**

### debian安装apt-get install rdesktop

安装成功后，在/usr/local/bin下生成了可执行的rdesktop 程序。

### 二、rdesktop 的使用

1.远程Windows 系统的设置

这里以连接Windows XP Professional的远程桌面服务为例。首先在WindowsXP 下启用远程桌面服务(注意，XP 的HomeEdition 没有远程桌面服务)，右键点击"我的电脑"，选择"属性"，查看"远程"选项，选择"允许用户远程连接到这台计算机"即可。

2.Linux 下rdesktop 的使用

rdesktop 的使用很简单，可通过#rdesktop-h得到使用的帮助。一般常用的登录命令为:

其中 "g 1024×768"设置分辨率为1024×768，"d 24"设置真彩24 位，hostname为 Windows 机器的主机名或者IP 地址。在输入了Windows XP的用户名和密码后，就可以登录并操作远程的Windows系统.


https://ci.freerdp.com/job/freerdp-nightly-windows/

freerdp         # Linux下的，an X11 Remote Desktop Protocol (RDP) client which is part of the FreeRDP project
wfreerdp.exe     # Windows下的，freerdp2.0 主程序，freerdp3.0将废弃
sdl-freerdp.exe  # Windows下的，freerdp3.0 主程序，其基于SDL库，目前Beta版还有一些Bug
————————————————
版权声明：本文为CSDN博主「开源技术」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/leopardsaga/article/details/133562802


Windows运行环境
Windows编译版的FreeRDP，是使用MinGW环境编译，所以wfreerdp.exe/sdl-freerdp.exe需要在MinGW环境下才能运行，即提供相关依赖库，否则直接退出不给提示。MobaXterm或Windows Git或直接安装MinGW都可以提供所需要库的Bash环境。下载地址：https://ci.freerdp.com/job/freerdp-nightly-windows/ ，包含文件：

winpr-makecert.exe
freerdp-proxy.exe
sdl-freerdp.exe
sfreerdp-server.exe
wfreerdp.exe
winpr-hash.exe
1
2
3
4
5
6
自适应远端桌面大小
问题：默认情况下，wfreerdp不管远程桌面大小，而是打开一个固定大小的窗口，得手动调整窗口的宽高以完整显示远端桌面。

解决：先在启动时添加/smart-sizing参数，然后在打开的窗口标题栏调出上下文菜单，取消smart sizing。窗口如此就自适应远端桌面大小了。

./wfreerdp.exe /u:<uname> /p:<pswd> /v:<host> /smart-sizing
————————————————
版权声明：本文为CSDN博主「开源技术」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/leopardsaga/article/details/133562802

