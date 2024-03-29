---
title: linux 报文高速捕获技术对比, napi/libpcap/afpacket/pfring/dpdk/xdp
author: "-"
date: 2021-10-31T04:20:17+00:00
url: libcap
categories:
  - network
tags:
  - reprint
---
## linux 报文高速捕获技术对比, napi/libpcap/afpacket/pfring/dpdk/xdp

1. 传统linux网络协议栈流程和性能分析
Linux网络协议栈是处理网络数据包的典型系统，它包含了从物理层直到应用层的全过程。

数据包到达网卡设备。
网卡设备依据配置进行DMA操作。 (第1次拷贝：网卡寄存器->内核为网卡分配的缓冲区 ring buffer）
网卡发送中断，唤醒处理器。
驱动软件从ring buffer中读取，填充内核skbuff结构 (第2次拷贝：内核网卡缓冲区ring buffer->内核专用数据结构skbuff）
数据报文达到内核协议栈，进行高层处理。
socket系统调用将数据从内核搬移到用户态。(第3次拷贝：内核空间->用户空间)
研究者们发现，Linux内核协议栈在数据包的收发过程中，内存拷贝操作的时间开销占了整个处理过程时间开销的65%，此外层间传递的系统调用时间也占据了8%～10%。

协议栈的主要问题:

针对单个数据包级别的资源分配和释放
每当一个数据包到达网卡，系统就会分配一个分组描述符用于存储数据包的信息和头部，直到分组传送到用户态空间，其描述符才被释放。此外，sk_buff 庞大的数据结构中的大部分信息对于大多数网络任务而言都是无用的.

流量的串行访问
现代网卡包括多个硬件的接收端扩展(receiver-side scaling, RSS)队列可以将分组按照五元组散列函数分配到不同的接收队列。使用这种技术，分组的捕获过程可以被并行化，因为每个RSS队列可以映射到一个特定的CPU核，并且可以对应相应的NAPI线程。这样整个捕获过程就可以做到并行化。
但是问题出现在之上的层次，Linux中的协议栈在网络层和传输层需要分析合并的所有数据包
①所有流量在一个单一模块中被处理，产生性能瓶颈；
②用户进程不能够从一个单一的RSS队列接收消息.
这就造成了上层应用无法利用现代硬件的并行化处理能力，这种在用户态分配流量先后序列的过程降低了系统的性能，丢失了驱动层面所获得的加速.
此外，从不同队列合并的流量可能会产生额外的乱序分组

从驱动到用户态的数据拷贝
从网卡收到数据包到应用取走数据的过程中，存在至少2次数据包的复制

内核到用户空间的上下文切换
从应用程序的视角来看，它需要执行系统调用来接收每个分组.每个系统调用包含一次从用户态到内核态的上下文切换，随之而来的是大量的CPU时间消耗.在每个数据包上执行系统调用时产生的上下文切换可能消耗近1 000个CPU周期.

跨内存访问
例如，当接收一个64 B分组时，cache未命中造成了额外13.8%的CPU周期的消耗.另外，在一个基于NUMA的系统中，内存访问的时间取决于访问的存储节点.因此，cache未命中在跨内存块访问环境下会产生更大的内存访问延迟，从而导致性能下降.

2. 提高捕获效率的技术
目前高性能报文捕获引擎中常用的提高捕获效率的技术，这些技术能够克服之前架构的性能限制.

预分配和重用内存资源
这种技术包括：
开始分组接收之前，预先分配好将要到达的数据包所需的内存空间用来存储数据和元数据(分组描述符).尤其体现在，在加载网卡驱动程序时就分配好 N 个描述符队列(每个硬件队列和设备一个).

同样，当一个数据包被传送到用户空间，其对应的描述符也不会被释放，而是重新用于存储新到达的分组.得益于这一策略，在每个数据包分配/释放所产生的性能瓶颈得到了消除.此外，也可以通过简化 sk_buff 的数据结构来减少内存开销.

数据包采用并行直接通道传递.
为了解决序列化的访问流量，需要建立从RSS队列到应用之间的直接并行数据通道.这种技术通过特定的RSS队列、特定的CPU核和应用三者的绑定来实现性能的提升.

这种技术也存在一些缺点:
①数据包可能会乱序地到达用户态，从而影响某些应用的性能;
②RSS使用Hash函数在每个接收队列间分配流量.当不同核的数据包间没有相互关联时，它们可以被独立地分析，但如果同一条流的往返数据包被分配到不同的CPU核上时，就会造成低效的跨核访问.

内存映射.
使用这种方法，应用程序的内存区域可以映射到内核态的内存区域，应用能够在没有中间副本的情况下读写这片内存区域.
用这种方式我们可以使应用直接访问网卡的DMA内存区域，这种技术被称为零拷贝.但零拷贝也存在潜在的安全问题，向应用暴露出网卡环形队列和寄存器会影响系统的安全性和稳定性 .

数据包的批处理.
为了避免对每个数据包的重复操作的开销，可以使用对数据包的批量处理.

这个策略将数据包划分为组，按组分配缓冲区，将它们一起复制到内核/用户内存.运用这种技术减少了系统调用以及随之而来的上下文切换的次数;同时也减少了拷贝的次数，从而减少了平摊到处理和复制每个数据包的开销.
但由于分组必须等到一个批次已满或定时器期满才会递交给上层，批处理技术的主要问题是延迟抖动以及接收报文时间戳误差的增加.

亲和性与预取.
由于程序运行的局部性原理，为进程分配的内存必须与正在执行它的处理器操作的内存块一致，这种技术被称为内存的亲和性.
CPU亲和性是一种技术，它允许进程或线程在指定的处理器核心上运行.
在内核与驱动层面，软件和硬件中断可以用同样的方法指定具体的CPU核或处理器来处理，称为中断亲和力.每当一个线程希望访问所接收的数据，如果先前这些数据已被分配到相同CPU核的中断处理程序接收，则它们在本地cache能够更容易被访问到.

3. 典型收包引擎
3.1 libpcap
参考：libpcap实现机制及接口函数

libpcap的包捕获机制是在数据链路层增加一个旁路处理，不干扰系统自身的网路协议栈的处理，对发送和接收的数据包通过Linux内核做过滤和缓冲处理，最后直接传递给上层应用程序。

数据包到达网卡设备。
网卡设备依据配置进行DMA操作。 (第1次拷贝：网卡寄存器->内核为网卡分配的缓冲区ring buffer）
网卡发送中断，唤醒处理器。
驱动软件从ring buffer中读取，填充内核skbuff结构 (第2次拷贝：内核网卡缓冲区ring buffer->内核专用数据结构skbuff）
接着调用netif_receive_skb函数：
5.1 如果有抓包程序，由网络分接口进入BPF过滤器，将规则匹配的报文拷贝到系统内核缓存  (第3次拷贝）。BPF为每一个要求服务的抓包程序关联一个filter和两个buffer。BPF分配buffer 且通常情况下它的额度是4KB the store buffer 被使用来接收来自适配器的数据； the hold buffer被使用来拷贝包到应用程序。
5.2 处理数据链路层的桥接功能；
5.3 根据skb->protocol字段确定上层协议并提交给网络层处理，进入网络协议栈，进行高层处理。
6. libpcap绕过了Linux内核收包流程中协议栈部分的处理，使得用户空间API可以直接调用 socket PF_PACKET从链路层驱动程序中获得数据报文的拷贝，将其从内核缓冲区拷贝至用户空间缓冲区 (第4次拷贝）

3.2 libpcap-mmap
libpcap-mmap是对旧的libpcap实现的改进，新版本的libpcap基本都采用packet_mmap机制。PACKET_MMAP通过mmap，减少一次内存拷贝 (第4次拷贝没有了），减少了频繁的系统调用，大大提高了报文捕获的效率。

### PF_RING

我们看到之前 libpcap 有4次内存拷贝。
libpcap_mmap 有3次内存拷贝。
PF_RING 提出的核心解决方案便是减少报文在传输过程中的拷贝次数。

我们可以看到，相对与 libpcap_mmap 来说，pfring 允许用户空间内存直接和 rx_buffer 做 mmap。
这又减少了一次拷贝 (libpcap_mmap 的第2次拷贝：rx_buffer->skb）

PF-RING ZC 实现了 DNA  (Direct NIC Access 直接网卡访问）技术，将用户内存空间映射到 rx_buffer。通过这样的方式，减少了一次拷贝 (libpcap的第3次拷贝，每个BPF过滤器有一个拷贝）。这就是零拷贝。

其缺点是，只有一个应用可以在某个时间打开DMA ring (请注意，现在的网卡可以具有多个RX / TX队列，从而就可以在每个队列上同时一个应用程序），换而言之，用户态的多个应用需要彼此沟通才能分发数据包。

### DPDK
参考：DPDK解析-----DPDK, PF_RING 对比
DPDK — IGB_UIO，与 UIO Framework 进行交互的内核模块

pf-ring zc和dpdk均可以实现数据包的零拷贝，两者均旁路了内核，但是实现原理略有不同。pf-ring zc通过zc驱动 (也在应用层）接管数据包，dpdk基于UIO实现。

一、UIO+mmap 实现零拷贝 (zero copy）
参考：The Userspace I/O HOWTO


一个设备驱动的主要任务有两个：

存取设备的内存
UIO 核心实现了mmap()可以处理物理内存(physical memory)，逻辑内存(logical memory)，
虚拟内存(virtual memory)。UIO驱动的编写是就不需要再考虑这些繁琐的细节。
处理设备产生的中断
对于设备中断的应答必须在内核空间进行。所以在内核空间有一小部分代码
用来应答中断和禁止中断，但是其余的工作全部留给用户空间处理。
如果用户空间要等待一个设备中断，它只需要简单的阻塞在对 /dev/uioX的read()操作上。当设备产生中断时，read()操作立即返回。

UIO 也实现了poll()系统调用，你可以使用select()来等待中断的发生。select()有一个超时参数可以用来实现有限时间内等待中断。

采用Linux提供UIO机制，可以旁路Kernel，将所有报文处理的工作在用户空间完成。

二、UIO+PMD 减少中断和CPU上下文切换

DPDK的UIO驱动屏蔽了硬件发出中断，然后在用户态采用主动轮询的方式，这种模式被称为PMD (Poll Mode Driver）。

与DPDK相比，pf-ring (no zc）使用的是NAPI polling和应用层polling，而pf-ring zc与DPDK类似，仅使用应用层polling。

三、HugePages 减少TLB miss

在操作系统引入MMU (Memory Management Unit）后，CPU读取内存的数据需要两次访问内存。第一次要查询页表将逻辑地址转换为物理地址，然后访问该物理地址读取数据或指令。

为了减少页数过多，页表过大而导致的查询时间过长的问题，便引入了TLB(Translation Lookaside Buffer)，可翻译为地址转换缓冲器。TLB是一个内存管理单元，一般存储在寄存器中，里面存储了当前最可能被访问到的一小部分页表项。

引入TLB后，CPU会首先去TLB中寻址，由于TLB存放在寄存器中，且其只包含一小部分页表项，因此查询速度非常快。若TLB中寻址成功 (TLB hit），则无需再去RAM中查询页表；若TLB中寻址失败 (TLB miss），则需要去RAM中查询页表，查询到后，会将该页更新至TLB中。

而DPDK采用HugePages ，在x86-64下支持2MB、1GB的页大小，大大降低了总页个数和页表的大小，从而大大降低TLB miss的几率，提升CPU寻址性能。

四、其它优化

SNA (Shared-nothing Architecture），软件架构去中心化，尽量避免全局共享，带来全局竞争，失去横向扩展的能力。NUMA体系下不跨Node远程使用内存。
SIMD (Single Instruction Multiple Data），从最早的mmx/sse到最新的avx2，SIMD的能力一直在增强。DPDK采用批量同时处理多个包，再用向量编程，一个周期内对所有包进行处理。比如，memcpy就使用SIMD来提高速度。
cpu affinity

3.6 XDP
参考：DPDK and XDP

xdp代表eXpress数据路径，使用ebpf 做包过滤，相对于dpdk将数据包直接送到用户态，用用户态当做快速数据处理平面，xdp是在驱动层创建了一个数据快速平面。
在数据被网卡硬件dma到内存，分配skb之前，对数据包进行处理。

请注意，XDP并没有对数据包做Kernel bypass，它只是提前做了一点预检而已。

相对于DPDK，XDP具有以下优点：

无需第三方代码库和许可
同时支持轮询式和中断式网络
无需分配大页
无需专用的CPU
无需定义新的安全网络模型
XDP的使用场景包括：

DDoS防御
防火墙
基于XDP_TX的负载均衡
网络统计
复杂网络采样
高速交易平台
4. 无锁队列技术
在报文捕获的流程中，无锁队列是一个很重要的数据结构。生产者 (网卡）写数据和消费者 (用户态程序）读数据，不加锁，能极大提升效率。

无锁队列实现主要依赖的技术有：

CAS原子指令操作
CAS (Compare and Swap，比较并替换）原子指令，用来保障数据的一致性。
指令有三个参数，当前内存值 V、旧的预期值 A、更新的值 B，当且仅当预期值 A和内存值 V相同时，将内存值修改为 B并返回true，否则什么都不做，并返回false。

内存屏障
执行运算的时候，每个CPU核心从内存读到各自的缓存中，结束后再从缓存更新到内存，这会引起线程间数据的不同步，故需要内存屏障强制把写缓冲区或高速缓存中的数据等写回主内存。
主要分为读屏障和写屏障：读屏障可以让 cache中的数据失效，强制重新从主内存加载数据；
写屏障能使cache 中的数据更新写入主内存。
在实现 valotitle关键字中就用到了内存屏障，从而保证线程A对此变量的修改，其他线程获取的值为最新的值。

5. 基于pfring/dpdk的应用
按照传统的观念，中间网络节点只能按照协议栈的层次一层一层地解析数据包，所谓路由器是三层设备，交换机是二层设备，防火墙分为二层防火墙和三层防火墙。

使用PF_RING/DPDK的设备，它可以将数据包直接从网卡的芯片DMA到你机器上的内存，然后你通过一个应用程序而不是内核协议栈来处理数据包。

至于说你的应用程序怎么处置数据包，我来列举几个：
1.深度解析数据包，按照各种你可以想到的粒度来解析会话，然后记录审计信息；

2.提供高性能的入侵检测功能；

3.转发数据包，按照路由器的方式。但是不再仅仅通过查询路由表的方式进行IP路由，而是可以通过各种各样的方式，转发表完全由你自己定义，比如实现一个通用的SDN流表；

4.根据上面第2点的含义，你可以决定哪些包被丢弃，这就是一个高性能的防火墙。

相比内核协议栈的串行解决方案，使用PF_RING/DPDK是一个更加高效的方案，不但高效，而且灵活。如果你拥有多核心的处理器，你甚至可以在用户态并行处理数据包的各个层信息。

参考：
http://crad.ict.ac.cn/fileup/HTML/2017-6-1300.shtml
https://coolshell.cn/articles/8239.html
https://cloud.tencent.com/developer/article/1521276
https://blog.csdn.net/dandelionj/article/details/16980571
https://my.oschina.net/moooofly/blog/898798
https://blog.csdn.net/dog250/article/details/77993218
————————————————
版权声明：本文为CSDN博主「网络安全研发随想」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/gengzhikui1992/article/details/103142848