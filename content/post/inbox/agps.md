---
title: AGPS
author: "-"
date: 2016-10-18T08:39:09+00:00
url: /?p=9310
categories:
  - Inbox
tags:
  - reprint
---
## AGPS

[http://www.cnblogs.com/magicboy110/archive/2010/12/12/1903927.html](http://www.cnblogs.com/magicboy110/archive/2010/12/12/1903927.html)

AGPS定位基本原理浅析
  
2010-12-12 21:03 by MagicBoy110, 34759 阅读, 18 评论, 收藏, 编辑位置服务已经成为越来越热的一门技术,也将成为以后所有移动设备 (智能手机、掌上电脑等) 的标配。随着人们对BLS(Based Location Serices,基于位置的服务)需求的飞速增长,无线定位技术也越来越得到重视。AGPS (Assisted GPS,A-GPS,网络辅助GPS) 定位技术结合了GPS定位和蜂窝基站定位的优势,借助蜂窝网络的数据传输功能,可以达到很高的定位精度和很快的定位速度,在移动设备尤其是手机中被越来越广泛的使用。本文以GSM网络辅助GPS定位为例对AGPS的定位原理进行简单介绍。

AGPS定位基本机制
  
根据定位媒介来分,定位技术基本包含基于GPS的定位和基于蜂窝基站的定位两类 (阅读本文前,建议先阅读《GPS定位基本原理浅析》和《GSM蜂窝基站定位基本原理浅析》两篇文章) 。GPS定位以其高精度得到更多的关注,但是其弱点也很明显: 一是硬件初始化 (首次搜索卫星) 时间较长,需要几分钟至十几分钟；二是GPS卫星信号穿透力若,容易受到建筑物、树木等的阻挡而影响定位精度。AGPS定位技术通过网络的辅助,成功的解决或缓解了这两个问题。对于辅助网络,有多种可能性,以GSM蜂窝网络为例,一般是通过GPRS网络进行辅助。

如上图所示,直接通过GPS信号从GPS获取定位所需的信息,这是传统GPS定位的基本机制。AGPS中,通过蜂窝基站的辅助来解决或缓解上文提到的两个问题:

对于第一个问题,首次搜星慢的问题,根据《GPS定位基本原理浅析》一文的介绍,我们知道是因为GPS卫星接收器需要进行全频段搜索以寻找GPS卫星而导致的。在AGPS中,通过从蜂窝网络下载当前地区的可用卫星信息 (包含当地区可用的卫星频段、方位、仰角等信息) ,从而避免了全频段大范围搜索,使首次搜星速度大大提高,时间由原来的几分钟减小到几秒钟。

对于第二个问题,GPS卫星信号易受干扰的问题,这是由GPS卫星信号本身的性质决定的,我们无法改变。但是APGS中,通过蜂窝基站参考GPS的辅助,或是借助GSM定位中Cell-ID定位 (COO定位) 方法的辅助,缓解了在GPS信号不良的情况下定位的问题,有效提高了在此情况下的定位精度。

AGPS定位基本流程
  
1.搜索卫星
  
AGPS定位仍然是基于GPS的,因此定位的首要步骤还是先搜索到当前地区的可用GPS卫星。在传统GPS定位中需要全频段搜索以找到可用卫星因而耗时较长,而AGPS通过网络直接下载当前地区的可用卫星信息,从而提高了搜星速度。同时,也减小了设备的电量消耗。

如上图所示,AGPS中从定位启动到GPS接收器找到可用卫星的基本流程如下:

 (1) 设备从蜂窝基站获取到当前所在的小区位置 (即一次COO定位)

 (2) 设备通过蜂窝网络将当前蜂窝小区位置传送给网络中的AGPS位置服务器

 (3) APGS位置服务器根据当前小区位置查询该区域当前可用的卫星信息 (包括卫星的频段、方位、仰角等相关信息) ,并返回给设备

 (4) GPS接收器根据得到的可用卫星信息,可以快速找到当前可用的GPS卫星

至此,GPS接收器已经可正常接收GPS信号,GPS初始化过程结束。AGPS对定位速度的提高就主要体现在此过程中。

2.计算位置
  
GPS接收器一旦找到四颗以上的可用卫星,就可以开始接收卫星信号实现定位。接下来的过程根据位置计算所在端的不同,通常有两种方案: 在移动设备端进行计算的MS-Based方式和在网络端进行计算的MS-Assisted方式。

MS-Based方式中,接下来过程与传统GPS定位完全相同,GPS接收器接收原始GPS信号,解调并进行一定处理,根据处理后的信息进行位置计算,得到最终的位置坐标。

MS-Assisted方式中,解调并处理后,接下来的过程如下图所示:

 (5) 设备将处理后的GPS信息 (伪距信息) 通过蜂窝网络传输给AGPS位置服务器

 (6) AGPS服务器根据伪距信息,并结合其他途径 (蜂窝基站定位、参考GPS定位等) 得到的辅助定位信息,计算出最终的位置坐标,返回给设备。

在此过程中可以看到,在使用MS-Assisted方式时,由于辅助定位信息的加入,可以取得更高的定位精度；同时,可以很大程度上克服弱GPS信号情况下的无法定位或精度降低的问题；将复杂计算转移到网络端,也可以很大程度上减小设备的电量消耗。

AGPS定位优劣分析
  
与传统GPS定位相比,APGS定位有如下的优势:

首次搜星速度快
  
有效减少设备的电量消耗
  
对于采用MS-Assisted方式的AGPS系统而言,除了以上优势外,还有:

定位精度更高
  
缓解弱GPS信号情况下无法定位或精度降低的问题
  
对移动设备的计算能力要求更低
  
当然,AGPS也有一定的限制:

必须有蜂窝网络 (GRRS/EDGE/CDMA等) 的支持用以数据传输,对一般用户而言可能需要为此支付一定的数据流量费用
  
必须有AGPS位置服务器的支持
  
与GPS一样,仍无法完美解决室内 (室内无法接收GPS信号) 定位的问题
  
AGPS定位技术的实际应用情况
  
因为AGPS需要网络支持,因此目前使用该技术的大部分设备为手机。

1.目前大部分支持AGPS的手机采用一种纯软件的AGPS方案。

该方案基于MS-Based位置计算方式。具体的方案为:

定期下载星历数据到手机中,手机中的AGPS软件会根据星历信息计算出当前位置的可用卫星信息,从而提供给设备用于快速搜星。用户可以选择通过WiFi、固网等免费网络定期更新星历数据,从而避免使用蜂窝网络产生的数据流量费用。当然,由于星历信息可能存在延迟,因此搜星时速度可能有所下降,但是仍然会比传统GPS定位快很多倍。

该方案的优点是纯软件,不需要专门的AGPS硬件,几乎所有GPS手机都可以使用；同时用户可以根据情况指定星历更新周期及更新方式,控制或减免蜂窝网络数据流量。

HTC的大部分AGPS手机都采用这种方案。如下图为HTC手机中用于更新卫星信息的"快速GPS"软件。

2.部分运营商的AGPS方案中,实施了在无GPS信号时自动切换到GSM蜂窝基站Cell-ID定位的措施,从而一定程度上解决了室内定位的问题。
  
如中国移动的OMA AGPS方案。

3.世界范围内一些AGPS芯片或相关服务已经广泛使用。
  
SiRF公司的AGPS芯片提供了硬件层次上的AGPS方案。

U-Blox的AssistNow A-GPS服务提供了AssistNow Online(在线AssistNow)和AssistNow OffLine(离线AssistNow)两种易用的AGPS方案。实际上这两种方案分别就是MS-Assisted和MS-Based两种定位计算方式的实现。

u-blox在线AssistNow系统组成。

u-blox离线AssistNow系统组成。

4.国内电信运营商的AGPS方案
  
中国移动正在制订的A-GPS方案基于OMA的SUPL规范,是一种用户平面的解决方案。

中国联通提供的gpsOne是MS-Assisted方式的A-GPS定位方案,也基于用户平面方式,目前只用于CDMA网络。

要了解二者的详细信息,请自行Google。下面仅列出几个可能有用的链接。

中国移动AGPS 技术引入策略
  
中国移动终端A-GPS技术规范3.0
  
2008-2009年中国移动位置服务(LBS)研究咨询报告
  
浅析 CDMA 移动通信网络下定位功能的实现

GPS定位、GSM蜂窝基站定位、AGPS定位是目前最流行的三种定位方式。AGPS定位基于GPS,却又借助蜂窝网络避免了GPS定位的两大软肋,无疑是最具竞争力的一种定位方案。随着3G时代的到来,BLS业务已成为各大运营商的必争之地,这其中,AGPS方案绝对是竞争中很重要的一部分。希望本文能让你对AGPS技术有一个大致的了解。
