---
title: Redis
author: "-"
date: 2015-07-13T09:06:18+00:00
url: /?p=8023
tags:
  - database
  - redis
  - cache
categories:
  - inbox
---
## Redis

REmote DIctionary Server(Redis) 是一个由SalvatoreSanfilippo写的key-value存储系统。

Redis是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库,并提供多种语言的API。从2010年3月15日起,Redis的开发工作由VMware主持。从2013年5月开始,Redis的开发由Pivotal赞助。redis是一个key-value存储系统。和Memcached类似,它支持存储的value类型相对更多,包括string(字符串)、list(链表)、set(集合)、zset(sorted set –有序集合)和hash (哈希类型) 。这些数据类型都支持push/pop、add/remove及取交集并集和差集及更丰富的操作,而且这些操作都是原子性的。在此基础上,redis支持各种不同方式的排序。与memcached一样,为了保证效率,数据都是缓存在内存中。区别的是redis会周期性的把更新的数据写入磁盘或者把修改操作写入追加的记录文件,并且在此基础上实现了 master-slave (主从)同步。
  
Redis 是一个高性能的key-value数据库。 redis的出现,很大程度补偿了memcached这类key/value存储的不足,在部分场合可以对关系数据库起到很好的补充作用。它提供了Java,C/C++,C#,PHP,JavaScript,Perl,Object-C,Python,Ruby,Erlang等客户端,使用很方便。
  
Redis支持主从同步。数据可以从主服务器向任意数量的从服务器上同步,从服务器可以是关联其他从服务器的主服务器。这使得Redis可执行单层树复制。存盘可以有意无意的对数据进行写操作。由于完全实现了发布/订阅机制,使得从数据库在任何地方同步树时,可订阅一个频道并接收主服务器完整的消息发布记录。同步对读取操作的可扩展性和数据冗余很有帮助。
  
redis的官网地址,非常好记,是redis.io。 (特意查了一下,域名后缀io属于国家域名,是british Indian Ocean territory,即英属印度洋领地)
  
目前,Vmware在资助着redis项目的开发和维护。redis[2] 的作者,叫Salvatore Sanfilippo,来自意大利的西西里岛,现在居住在卡塔尼亚。目前供职于Pivotal公司。他使用的网名是antirez。

Redis 4.0 之后的版本,情况就有了一些变动,新版的 Redis 服务在执行一些命令时就会使用『主处理线程』之外的其他线程,例如 UNLINK、FLUSHALL ASYNC, FLUSHDB ASYNC 等非阻塞的删除操作。
Redis 在较新的版本中引入了多线程,不过是在部分命令上引入的,其中包括非阻塞的删除操作,在整体的架构设计上,主处理程序还是单线程模型的

### 线程模型

使用单线程模型能带来更好的可维护性,方便开发和调试；  
使用单线程模型也能并发的处理客户端的请求；  
Redis 服务中运行的绝大多数操作的性能瓶颈都不是 CPU

#### 6.0

Redis 的多线程部分只是用来处理网络数据的读写和协议解析,执行命令仍然是单线程。之所以这么设计是不想因为多线程而变得复杂
I/O 多路复用的主要作用是让我们可以使用一个线程来监控多个连接是否可读或者可写,但是从网络另一头发的数据包需要先解序列化成 Redis 内部其他模块可以理解的命令,这个过程就是 Redis 6.0 引入多线程来并发处理的。

I/O 多路复用模块收到数据包之后将其丢给后面多个 I/O 线程进行解析,I/O 线程处理结束后,主线程会负责串行的执行这些命令,由于向客户端发回数据包的过程也是比较耗时的,所以执行之后的结果也会交给多个 I/O 线程发送回客户端。

### AOF

AOF 是 Redis 的一种持久化机制,它会在每次收到来自客户端的写请求时,将其记录到日志中,每次 Redis 服务器启动时都会重放 AOF 日志构建原始的数据集,保证数据的持久性。

### Keyspace Notifications, 键空间通知

在Redis2.8.0版本的时候,推出 Keyspace Notifications future。   Keyspace Notifications 此特性允许客户端可以以 订阅/发布 (Sub/Pub) 模式,接收那些对数据库中的键和值有影响的操作事件。这些操作事件具体来说,就是 hash , del, expire , set , lpop 等。
那么你可能会问,redis keyspace 到底有啥用处？   简单说,对于我个人主要关注keyspace几个扩展场景:

1. 类似审计或者监控的场景.
2. 通过expire来实现不可靠的定时器.
3. 借助expire来实现不可靠的注册发现.  

为什么说是不可靠的实现?

首先Redis Pub/Sub 是一种并不可靠地消息机制,他不会做信息的存储,只是在线转发,那么肯定也没有ack确认机制,另外只有订阅段监听时才会转发!!!    所以Keyspace Notification 也不是可靠地通知系统,如果你的系统需要很好的可靠性,那么Keyspace Notification可能并不是一种很好的选择。

默认情况下,Redis 并不会开启Keyspace Notification, 我们可以通过修改redis.conf的notify-keyspace-events 或者使用CONFIG SET命令来开启该功能,设置参数,来开启全部或者部分通知

### slot

slot 是什么
我们都知道在集群模式下key是需要进行路由的,那就需要有路由策略,Redis Cluster并没有使用一致性hash的方案,而是使用分配slot的方式进行key路由。

如何分配slot
Redis Cluster为整个集群定义了一共16384个slot,并通过crc16的hash函数来对key进行取模,将结果路由到预先分配过slot的相应节点上。

1. 将节点加入Redis Cluster中
2. 为集群中的节点分配slot(分配完成后,每个即诶单不仅直到自己的slot列表,还需要知道别的节点的slot列表)
3. 分配完成后,key会根据crc16计算出得结果和16384取模进行slot定位,从而定位到具体节点。
举个例子
节点A保存了0-5500的slot
节点B保存了5501-11000的slot
节点C保存了11001-16383的slot
当客户端要执行 SET test 123 实际上会执行crc16(123)
具体流程如下:
客户端调用SET test 123
任意节点接受到指令,将执行crc16(test)=63534
63534%16384=14382
14382术语节点C因此将key test路由到C节点。
因为指令是发到任意节点的,如果这个节点不是C节点,则会触发MOVED命令,将命令发到相应node(各个节点保存了其他节点的slot列表)

重新分配slot
所有分片的算法都会面对一个问题,就是当节点增加或减少时怎么处理,Redis Cluster也不例外
当有节点D加入进来原本的A,B,C节点需要拿出一部分slot给到D,这样的操作就叫做slot重新分配。

redis-trib
redis Cluster 是使用redis-trib来自动实现的slot重新分配

### Redis是单线程的,但Redis为什么这么快？

leo_su

近乎所有与Java相关的面试都会问到缓存的问题,基础一点的会问到什么是“二八定律”、什么是“热数据和冷数据”,复杂一点的会问到缓存雪崩、缓存穿透、缓存预热、缓存更新、缓存降级等问题,这些看似不常见的概念,都与我们的缓存服务器相关,一般常用的缓存服务器有Redis、Memcached等,而笔者目前最常用的也只有Redis这一种。

如果你在以前面试的时候还没有遇到过面试官问你《为什么说Redis是单线程的以及Redis为什么这么快！》,那么你看到这篇文章的时候,你应该觉得是一件很幸运的事情！如果你刚好是一位高逼格的面试官,你也可以拿这道题去面试对面“望穿秋水”般的小伙伴,测试一下他的掌握程度。

好啦！步入正题！我们先探讨一下Redis是什么,Redis为什么这么快、然后在探讨一下为什么Redis是单线程的？

#### Redis简介

Redis是一个开源的内存中的数据结构存储系统,它可以用作: 数据库、缓存和消息中间件。

它支持多种类型的数据结构,如字符串 (String) ,散列 (Hash) ,列表 (List) ,集合 (Set) ,有序集合 (Sorted Set或者是ZSet) 与范围查询,Bitmaps,Hyperloglogs 和地理空间 (Geospatial) 索引半径查询。其中常见的数据结构类型有: String、List、Set、Hash、ZSet这5种。

Redis 内置了复制 (Replication) ,LUA脚本 (Lua scripting) , LRU驱动事件 (LRU eviction) ,事务 (Transactions)  和不同级别的磁盘持久化 (Persistence) ,并通过 Redis哨兵 (Sentinel) 和自动分区 (Cluster) 提供高可用性 (High Availability) 。

Redis也提供了持久化的选项,这些选项可以让用户将自己的数据保存到磁盘上面进行存储。根据实际情况,可以每隔一定时间将数据集导出到磁盘 (快照) ,或者追加到命令日志中 (AOF只追加文件) ,他会在执行写命令时,将被执行的写命令复制到硬盘里面。您也可以关闭持久化功能,将Redis作为一个高效的网络的缓存数据功能使用。

Redis不使用表,他的数据库不会预定义或者强制去要求用户对Redis存储的不同数据进行关联。

数据库的工作模式按存储方式可分为: 硬盘数据库和内存数据库。Redis 将数据储存在内存里面,读写数据的时候都不会受到硬盘 I/O 速度的限制,所以速度极快。

 (1) 硬盘数据库的工作模式:

图片描述

 (2) 内存数据库的工作模式:

图片描述

看完上述的描述,对于一些常见的Redis相关的面试题,是否有所认识了,例如: 什么是Redis、Redis常见的数据结构类型有哪些、Redis是如何进行持久化的等。

二.Redis到底有多快？

Redis采用的是基于内存的采用的是单进程单线程模型的 KV 数据库,由C语言编写,官方提供的数据是可以达到100000+的QPS (每秒内查询次数) 。

这个数据不比采用单进程多线程的同样基于内存的 KV 数据库 Memcached 差！

图片描述

横轴是连接数,纵轴是QPS。此时,这张图反映了一个数量级,希望大家在面试的时候可以正确的描述出来,不要问你的时候,你回答的数量级相差甚远！

三.Redis为什么这么快？

1. 完全基于内存,绝大部分请求是纯粹的内存操作,非常快速。数据存在内存中,类似于HashMap,HashMap的优势就是查找和操作的时间复杂度都是O(1)；

2. 数据结构简单,对数据操作也简单,Redis中的数据结构是专门进行设计的；

3. 采用单线程,避免了不必要的上下文切换和竞争条件,也不存在多进程或者多线程导致的切换而消耗 CPU,不用去考虑各种锁的问题,不存在加锁释放锁操作,没有因为可能出现死锁而导致的性能消耗；

4. 使用多路I/O复用模型,非阻塞IO；

5. 使用底层模型不同,它们之间底层实现方式以及与客户端之间通信的应用协议不一样,Redis直接自己构建了VM 机制 ,因为一般的系统调用系统函数的话,会浪费一定的时间去移动和请求；

以上几点都比较好理解,下边我们针对多路 I/O 复用模型进行简单的探讨:

 (1) 多路 I/O 复用模型

多路I/O复用模型是利用 select、poll、epoll 可以同时监察多个流的 I/O 事件的能力,在空闲的时候,会把当前线程阻塞掉,当有一个或多个流有 I/O 事件时,就从阻塞态中唤醒,于是程序就会轮询一遍所有的流 (epoll 是只轮询那些真正发出了事件的流) ,并且只依次顺序的处理就绪的流,这种做法就避免了大量的无用操作。

这里“多路”指的是多个网络连接,“复用”指的是复用同一个线程。

采用多路 I/O 复用技术可以让单个线程高效的处理多个连接请求 (尽量减少网络 IO 的时间消耗) ,且 Redis 在内存中操作数据的速度非常快,也就是说内存内的操作不会成为影响Redis性能的瓶颈,主要由以上几点造就了 Redis 具有很高的吞吐量。

四.那么为什么Redis是单线程的？

我们首先要明白,上边的种种分析,都是为了营造一个Redis很快的氛围！官方FAQ表示,因为Redis是基于内存的操作,CPU不是Redis的瓶颈,Redis的瓶颈最有可能是机器内存的大小或者网络带宽。既然单线程容易实现,而且CPU不会成为瓶颈,那就顺理成章地采用单线程的方案了 (毕竟采用多线程会有很多麻烦！) 。

看到这里,你可能会气哭！本以为会有什么重大的技术要点才使得Redis使用单线程就可以这么快,没想到就是一句官方看似糊弄我们的回答！但是,我们已经可以很清楚的解释了为什么Redis这么快,并且正是由于在单线程模式的情况下已经很快了,就没有必要在使用多线程了！

但是,我们使用单线程的方式是无法发挥多核CPU 性能,不过我们可以通过在单机开多个Redis 实例来完善！

警告1: 这里我们一直在强调的单线程,只是在处理我们的网络请求的时候只有一个线程来处理,一个正式的Redis Server运行的时候肯定是不止一个线程的,这里需要大家明确的注意一下！例如Redis进行持久化的时候会以子进程或者子线程的方式执行 (具体是子线程还是子进程待读者深入研究) ；例如我在测试服务器上查看Redis进程,然后找到该进程下的线程:

图片描述

ps命令的“-T”参数表示显示线程 (Show threads, possibly with SPID column.) “SID”栏表示线程ID,而“CMD”栏则显示了线程名称。

警告2: 在上图中FAQ中的最后一段,表述了从Redis 4.0版本开始会支持多线程的方式,但是,只是在某一些操作上进行多线程的操作！所以该篇文章在以后的版本中是否还是单线程的方式需要读者考证！

### 注意点

1. 我们知道Redis是用”单线程-多路复用IO模型”来实现高性能的内存数据服务的,这种机制避免了使用锁,但是同时这种机制在进行sunion之类的比较耗时的命令时会使redis的并发下降。

因为是单一线程,所以同一时刻只有一个操作在进行,所以,耗时的命令会导致并发的下降,不只是读并发,写并发也会下降。而单一线程也只能用到一个CPU核心,所以可以在同一个多核的服务器中,可以启动多个实例,组成master-master或者master-slave的形式,耗时的读命令可以完全在slave进行。

需要改的redis.conf项:

pidfile /var/run/redis/redis_6377.pid #pidfile要加上端口号

port 6377 #这个是必须改的

logfile /var/log/redis/redis_6377.log #logfile的名称也加上端口号

dbfilename dump_6377.rdb #rdbfile也加上端口号

2. “我们不能任由操作系统负载均衡,因为我们自己更了解自己的程序,所以,我们可以手动地为其分配CPU核,而不会过多地占用CPU,或是让我们关键进程和一堆别的进程挤在一起。”

CPU 是一个重要的影响因素,由于是单线程模型,Redis 更喜欢大缓存快速 CPU, 而不是多核。

在多核 CPU 服务器上面,Redis 的性能还依赖NUMA 配置和处理器绑定位置。最明显的影响是 redis-benchmark 会随机使用CPU内核。为了获得精准的结果,需要使用固定处理器工具 (在 Linux 上可以使用 taskset) 。最有效的办法是将客户端和服务端分离到两个不同的 CPU 来高校使用三级缓存。

六.扩展

以下也是你应该知道的几种模型,祝你的面试一臂之力！

1. 单进程多线程模型: MySQL、Memcached、Oracle (Windows版本) ；

2. 多进程模型: Oracle (Linux版本) ；

3. Nginx有两类进程,一类称为Master进程(相当于管理进程),另一类称为Worker进程 (实际工作进程) 。启动方式有两种:

 (1) 单进程启动: 此时系统中仅有一个进程,该进程既充当Master进程的角色,也充当Worker进程的角色。

 (2) 多进程启动: 此时系统有且仅有一个Master进程,至少有一个Worker进程工作。

 (3) Master进程主要进行一些全局性的初始化工作和管理Worker的工作；事件处理是在Worker中进行的。

---

[http://blog.csdn.net/rockstar541/article/details/30245493](http://blog.csdn.net/rockstar541/article/details/30245493)
  
[https://www.zhihu.com/question/20698365](https://www.zhihu.com/question/20698365)
  
[https://www.cnblogs.com/zhoujinyi/p/11606935.html](https://www.cnblogs.com/zhoujinyi/p/11606935.html)
  
[https://blog.51cto.com/zengestudy/1853801](https://blog.51cto.com/zengestudy/1853801)
[https://draveness.me/whys-the-design-redis-single-thread/](https://draveness.me/whys-the-design-redis-single-thread/)  
[http://xiaorui.cc/archives/4123](http://xiaorui.cc/archives/4123)  

[http://redisbook.com/](http://redisbook.com/)

[https://segmentfault.com/a/1190000017375843](https://segmentfault.com/a/1190000017375843)
