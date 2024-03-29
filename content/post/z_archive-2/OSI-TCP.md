---
title: OSI 七层协议, TCP/IP 四层协议, osi 7层, tcp/ip 4层
author: "-"
date: 2016-04-24T02:07:00+00:00
url: osi
categories:
  - network
tags:
  - reprint
---

## OSI 七层协议, TCP/IP 四层协议, osi 7层, tcp/ip 4层

## OSI 七层协议和TCP/IP四层协议之比较

### TCP/IP四层协议

1. 数据链路层, Data Link Layer: ARP, RARP, PPPoE (Ethernet 以太网协议,MAC 地址) , 数据链路层和物理层合并为网络接口层
2. 网络层, Network Layer: IP,ICMP,IGMP  (IPv4 / IPv6 协议,IP 地址)
3. 传输层: TCP(网络端口),UDP,UGP, QUIC, SCTP (流控制传输协议)
4. 应用层: Telnet,FTP,SMTP,SNMP

### OSI 七层协议

1. 物理层  Physical Layer: EIA/TIA-232, EIA/TIA-499, V.35, V.24, RJ45, Ethernet, 802.3, 8025, FDDI, NRZI, NRZ, B8ZS
2. 数据链路层 Data Link Layer : Frame Relay, HDLC, PPP, IEEE 802.3/802.2, FDDI, ATM, IEEE 8025/802.2
3. 网络层 Network Layer: IP, IPX, AppleTalk DDP
4. 传输层 Transport Layer: TCP, UDP, SPX, QUIC, SCTP (流控制传输协议)
5. 会话层 Session Layer: RPC, SQL, NFS, NetBIOS, names, AppleTalk, ASP, DECnet, SCP
6. 表示层 Presentation Layer :TIFF,GIF,JPEG,PICT,ASCII,EBCDIC,encryption,MPEG,MIDI,HTML
7. 应用层 Application Layer : FTP, WWW, Telnet, NFS, SMTP, Gateway, SNMP

#### 应用层

为特定应用程序提供数据传输服务,例如 HTTP、DNS 等协议。数据单位为报文。  

1. 主要功能: 用户接口、应用程序
2. 典型设备: 网关
3. 典型协议、标准和应用: TELNET, FTP, HTTP

#### 表示层

1. 主要功能 : 数据的表示、压缩和加密
2. 典型设备: 网关
3. 典型协议、标准和应用: ASCLL、PICT、TIFF、JPEG、 MIDI、MPEG

#### 会话层

1. 主要功能 : 会话的建立和结束
2. 典型设备: 网关
3. 典型协议、标准和应用: RPC、SQL、NFS 、X WINDOWS、ASP

#### 传输层

为进程提供通用数据传输服务。由于应用层协议很多,定义通用的传输层协议就可以支持不断增多的应用层协议。传输层包括两种协议: 传输控制协议 TCP,提供面向连接、可靠的数据传输服务,数据单位为报文段；用户数据报协议 UDP,提供无连接、尽最大努力的数据传输服务,数据单位为用户数据报。TCP 主要提供完整性服务,UDP 主要提供及时性服务。  

1. 主要功能 : 端到端控制
2. 典型设备: 网关
3. 典型协议、标准和应用: TCP、UDP、SPX

#### 网络层

因为网络层是整个互联网的核心, 因此应当让网络层尽可能简单。网络层向上只提供简单灵活的、无连接的、尽最大努力交互的数据报服务。

使用 IP 协议, 可以把异构的物理网络连接起来,使得在网络层看起来好像是一个统一的网络。

为主机提供数据传输服务。而传输层协议是为主机中的进程提供数据传输服务。网络层把传输层传递下来的报文段或者用户数据报封装成分组。  

1. 主要功能 : 路由,寻址
2. 典型设备: 路由器
3. 典型协议、标准和应用: IP, IPX、APPLETALK、ICMP

#### 数据链路层

MAC 地址是链路层地址, 长度为 6 字节 (48 位) ,用于唯一标识网络适配器 (网卡) 。

网络层针对的还是主机之间的数据传输服务,而主机之间可以有很多链路,链路层协议就是为同一链路的主机提供数据传输服务。数据链路层把网络层传下来的分组封装成帧。  

1. 主要功能 : 保证无差错的数据链路
2. 典型设备: 交换机、网桥、网卡
3. 典型协议、标准和应用: 802.2、802.3ATM、HDLC、FRAME RELAY

#### 物理层

考虑的是怎样在传输媒体上传输数据比特流,而不是指具体的传输媒体。物理层的作用是尽可能屏蔽传输媒体和通信手段的差异,使数据链路层感觉不到这些差异。

1. 主要功能 : 传输比特流
2. 典型设备: 集线器、中继器
3. 典型协议、标准和应用: V.35、EIA/TIA-232

从下到上,物理层最低的！！！！应用层最高。

### TCP/IP(Transmission Control Protocol/Internet Protocol,传输控制协议/网间网协议)

TCP/IP(Transmission Control Protocol/Internet Protocol,传输控制协议/网间网协议)是目前世界上应用最为广泛的协议,它的流行与Internet的迅猛发展密切相关—TCP/IP最初是为互联网的原型ARPANET所设计的,目的是提供一整套方便实用、能应用于多种网络上的协议,事实证明TCP/IP做到了这一点,它使网络互联变得容易起来,并且使越来越多的网络加入其中,成为Internet的事实标准。

TCP/IP层次模型共分为四层: 应用层、传输层、网络层、数据链路层。  

* 应用层—应用层是所有用户所面向的应用程序的统称。TCP/IP协议族在这一层面有着很多协议来支持不同的应用,许多大家所熟悉的基于Internet的应用的实现就离不开这些协议。如我们进行万维网 (WWW) 访问用到了HTTP协议、文件传输用FTP协议、电子邮件发送用SMTP、域名的解析用DNS协议、远程登录用Telnet协议等等,都是属于TCP/IP应用层的；就用户而言,看到的是由一个个软件所构筑的大多为图形化的操作界面,而实际后台运行的便是上述协议。
* 传输层—这一层的的功能主要是提供应用程序间的通信,TCP/IP协议族在这一层的协议有TCP和UDP。
* 网络层—是TCP/IP协议族中非常关键的一层,主要定义了IP地址格式,从而能够使得不同应用类型的数据在Internet上通畅地传输,IP协议就是一个网络层协议。
* 网络接口层—这是TCP/IP软件的最低层,负责接收IP数据包并通过网络发送之,或者从网络上接收物理帧,抽出IP数据报,交给IP层。

### TCP/UDP协议

TCP (Transmission Control Protocol)和UDP(User Datagram Protocol)协议属于传输层协议。其中TCP提供IP环境下的数据可靠传输,它提供的服务包括数据流传送、可靠性、有效流控、全双工操作和多路复用。通过面向连接、端到端和可靠的数据包发送。通俗说,它是事先为所发送的数据开辟出连接好的通道,然后再进行数据发送；而UDP则不为IP提供可靠性、流控或差错恢复功能。一般来说,TCP对应的是可靠性要求高的应用,而UDP对应的则是可靠性要求低、传输经济的应用。TCP支持的应用协议主要有: Telnet、FTP、SMTP等；UDP支持的应用层协议主要有: NFS (网络文件系统) 、SNMP (简单网络管理协议) 、DNS (主域名称系统) 、TFTP (通用文件传输协议) 等。

IP协议的定义、IP地址的分类及特点

什么是IP协议,IP地址如何表示,分为几类,各有什么特点？
  
为了便于寻址和层次化地构造网络,IP地址被分为A、B、C、D、E五类,商业应用中只用到A、B、C三类。

IP协议(Internet Protocol)又称互联网协议,是支持网间互连的数据报协议,它与TCP协议 (传输控制协议) 一起构成了TCP/IP协议族的核心。它提供网间连接的完善功能, 包括IP数据报规定互连网络范围内的IP地址格式。

Internet 上,为了实现连接到互联网上的结点之间的通信,必须为每个结点 (入网的计算机) 分配一个地址,并且应当保证这个地址是全网唯一的,这便是IP地址。

目前的IP地址 (IPv4: IP第4版本) 由32个二进制位表示,每8位二进制数为一个整数,中间由小数点间隔,如159.226.41.98,整个IP地址空间有4组8位二进制数,由表示主机所在的网络的地址 (类似部队的编号) 以及主机在该网络中的标识 (如同士兵在该部队的编号) 共同组成。

为了便于寻址和层次化的构造网络,IP地址被分为A、B、C、D、E五类,商业应用中只用到A、B、C三类。

* A类地址: A类地址的网络标识由第一组8位二进制数表示,网络中的主机标识占3组8位二进制数,A类地址的特点是网络标识的第一位二进制数取值必须为 "0"。不难算出,A类地址允许有126个网段,每个网络大约允许有1670万台主机,通常分配给拥有大量主机的网络 (如主干网) 。
* B类地址: B类地址的网络标识由前两组8位二进制数表示,网络中的主机标识占两组8位二进制数,B类地址的特点是网络标识的前两位二进制数取值必须为"10"。B类地址允许有16384个网段,每个网络允许有65533台主机,适用于结点比较多的网络 (如区域网) 。

* C类地址: C类地址的网络标识由前3组8位二进制数表示,网络中主机标识占1组8位二进制数,C类地址的特点是网络标识的前3位二进制数取值必须为"110"。具有C类地址的网络允许有254台主机,适用于结点比较少的网络 (如校园网) 。

为了便于记忆,通常习惯采用4个十进制数来表示一个IP地址,十进制数之间采用句点"."予以分隔。这种IP地址的表示方法也被称为点分十进制法。如以这种方式表示,A类网络的IP地址范围为1.0.0.1－127.255.255.254；B类网络的IP地址范围为: 128.1.0.1－191.255.255.254；C类网络的IP地址范围为: 192.0.1.1－223.255.255.254。

由于网络地址紧张、主机地址相对过剩,采取子网掩码的方式来指定网段号。

TCP/IP协议与低层的数据链路层和物理层无关,这也是TCP/IP的重要特点。正因为如此 ,它能广泛地支持由低两层协议构成的物理网络结构。目前已使用TCP/IP连接成洲际网、全国网与跨地区网。

OSP与TCP/IP的参考层次图:

OSP与TCP/IP的比较:

分层结构
  
OSI参考模型与TCP/IP协议都采用了分层结构,都是基于独立的协议栈的概念。OSI参考模型有7层,而TCP/IP协议只有4层,即TCP/IP协议没有了表示层和会话层,并且把数据链路层和物理层合并为网络接口层。不过,二者的分层之间有一定的对应关系

标准的特色
  
OSI参考模型的标准最早是由ISO和CCITT (ITU的前身) 制定的,有浓厚的通信背景,因此也打上了深厚的通信系统的特色,比如对服务质量 (QoS) 、差错率的保证,只考虑了面向连接的服务。并且是先定义一套功能完整的构架,再根据该构架来发展相应的协议与系统。

TCP/IP协议产生于对Internet网络的研究与实践中,是应实际需求而产生的,再由IAB、IETF等组织标准化,而并不是之前定义一个严谨的框架。而且TCP/IP最早是在UNIX系统中实现的,考虑了计算机网络的特点,比较适合计算机实现和使用。

连接服务
  
OSI的网络层基本与TCP/IP的网际层对应,二者的功能基本相似,但是寻址方式有较大的区别。

OSI的地址空间为不固定的可变长,由选定的地址命名方式决定,最长可达160byte,可以容纳非常大的网络,因而具有较大的成长空间。根据OSI的规定,网络上每个系统至多可以有256个通信地址。

TCP/IP网络的地址空间为固定的4byte (在目前常用的IPV4中是这样,在IPV6中将扩展到16byte) 。网络上的每一个系统至少有一个唯一的地址与之对应。

传输服务
  
OSI与TCP/IP的传输层都对不同的业务采取不同的传输策略。OSI定义了五个不同层次的服务: TP1,TP2,TP3,TP4,TP5。TCP/IP定义了TCP和UPD两种协议,分别具有面向连接和面向无连接的性质。其中TCP与OSI中的TP4,UDP与OSI中的TP0在构架和功能上大体相同,只是内部细节有一些差异。

应用范围
  
OSI 由于体系比较复杂, 而且设计先于实现, 有许多设计过于理想, 不太方便计算机软件实现, 因而完全实现 OSI 参考模型的系统并不多, 应用的范围有限。 而 TCP/IP 协议最早在计算机系统中实现, 在 UNIX、Windows 平台中都有稳定的实现, 并且提供了简单方便的编程接口 (API) , 可以在其上开发出丰富的应用程序,因此得到了广泛的应用。TCP/IP协议已成为目前网际互联事实上的国际标准和工业标准。

>[http://blog.sina.com.cn/s/blog_611a32ff0100gxkg.html](http://blog.sina.com.cn/s/blog_611a32ff0100gxkg.html)
