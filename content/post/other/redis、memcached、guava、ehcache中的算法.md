---
title: Redis, Memcache, Guava, Ehcache 中的算法
author: "-"
date: 2015-06-28T03:19:50+00:00
url: /?p=7929
categories:
  - Algorithm
tags:
  - redis
  - memcache
  - cache

---
## Redis, Memcache, Guava, Ehcache 中的算法

缓存那些事，一是内存爆了要用LRU(最近最少使用, Least Recently Used)、LFU(最少访问次数, Least Frequently Used)、FIFO的算法清理一些；二是设置了超时时间的键过期便要删除，用主动或惰性的方法。

在看所有的细节之前，可以看一篇相当专业的《缓存算法》，世界真宽阔，算法真奇妙。

### LRU

简单粗暴的Redis
  
今天看Redis3.0的发行通告里说，LRU算法大幅提升了，就翻开源码来八卦一下，结果哭笑不得，这旧版的"近似LRU"算法，实在太简单，太偷懒，太Redis了。

在Github的Redis项目里搜索lru，找到代码在redis.c的freeMemoryIfNeeded()函数里。

先看2.6版的代码:  竟然就是随机找三条记录出来，比较哪条空闲时间最长就删哪条，然后再随机三条出来，一直删到内存足够放下新记录为止.......可怜我看配置文档后的想象，一直以为它会帮我在整个Redis里找空闲时间最长的，哪想到我有一百万条记录的时候，它随便找三条就开始删了。

好，收拾心情再看3.0版的改进: 现在每次随机五条记录出来，插入到一个长度为十六的按空闲时间排序的队列里，然后把排头的那条删掉，然后再找五条出来，继续尝试插入队列.........嗯，好了一点点吧，起码每次随机多了两条，起码不只在一次随机的五条里面找最久那条，会连同之前的一起做比较......

中规中矩的Memcached
  
相比之下，Memcached实现的是再标准不过的LRU算法，专门使用了一个教科书式的双向链表来存储slab内的LRU关系，代码在item.c里，详见memcached源码分析--LRU队列与item结构体，元素插入时把自己放到列头，删除时把自己的前后两个元素对接起来，更新时先做删除再做插入。

分配内存超限时，很自然就会从LRU的队尾开始清理。

同样中规中矩的Guava Cache
  
Guava Cache同样做了一个双向的Queue，见LocalCache中的AccessQueue类，也会在超限时从Queue的队尾清理，见evictEntries()函数。

和Redis旧版一样的Ehcache/Hazelcast
  
看文档，居然和Redis2.6一样，直接随机8条记录，找出最旧那条，刷到磁盘里，再看代码，Eviction类 和 OnHeapStore的evict()函数。

再看Hazelcast，几乎一样，随机取25条。 这种算法，切换到LFU也非常简单。

小结
  
不过后来再想想，也许Redis本来就不是主打做Cache的，这种内存爆了需要通过LRU删掉一些元素不是它的主要功能，默认设置都是noeviction——内存不够直接报错的，所以就懒得建个双向链表，而且每次访问时都要更新它了，看Google Group里长长的讨论，新版算法也是社区智慧的结晶。何况，Ehcache和Hazelcast也是和它的旧版一样的算法，Redis的新版还比这两者强了。

后来，根据@刘少壮同学的提示，JBoss的InfiniSpan里还实现了比LRU更高级的LIRS算法，可以避免一些冷数据因为某个原因被大量访问后，把热数据挤占掉。

### 过期键删除

如果能为每一个设置了过期的元素启动一个Timer，一到时间就触发把它删掉，那无疑是能最快删除过期键最省空间的，在Java里用一条DeplayQueue存着，开条线程不断的读取就能做到。但因为该线程消耗CPU较多，在内存不紧张时有点浪费，似乎大家都不用这个方法。

所以有了惰性检查，就是每次元素被访问时，才去检查它是否已经超时了，这个各家都一样。但如果那个元素后来都没再被访问呢，会永远占着位子吗？所以各家都再提供了一个定期主动删除的方式。

Redis
  
代码在redis.c的activeExpireCycle()里，看过文档的人都知道，它会在主线程里，每100毫秒执行一次，每次随机抽20条Key检查，如果有1/4的键过期了，证明此时过期的键可能比较多，就不等100毫秒，立刻开始下一轮的检查。不过为免把CPU时间都占了，又限定每轮的总执行时间不超过1毫秒。

Memcached
  
Memcached里有个文不对题的LRU爬虫线程，利用了之前那条LRU的队列，可以设置多久跑一次(默认也是100毫秒)，沿着列尾一直检查过去，每次检查LRU队列中的N条数据。虽然每条Key设置的过期时间可能不一样，但怎么说命中率也比Redis的随机选择N条数据好一点，但它没有Redis那种过期的多了立马展开下一轮检查的功能，所以每秒最多只能检查10N条数据，需要自己自己权衡N的设置。

Guava Cache
  
在Guava Cache里，同一个Cache里所有元素的过期时间是一样的，所以它比Memached更方便，顺着之前那条LRU的Queue检查超时，不限定个数，直到不超时为止。而且它这个检查的调用时机并不是100毫秒什么的，而是每次各种写入数据时的preWriteCleanup()方法中都会调用。

吐槽一句，Guava的Localcache类里面已经4872行了，一点都不轻量了。

Ehcache
  
Ehcache更乱，首先它的内存存储中只有惰性检查，没有主动检查过期的，只会在内存超限时不断用近似LRU算法(见上)把内存中的元素刷到磁盘中，在文件存储中才有超时检查的线程，FAQ里专门解释了原因。

然后磁盘存储那有一条8小时左右跑一次的线程，每次遍历所有元素.....见DiskStorageFactory里的DiskExpiryTask。 一圈看下来，Ehcache的实现最弱。
  
文章持续修改，转载时请保留原链接:  [http://calvin1978.blogcn.com/articles/lru.html](http://calvin1978.blogcn.com/articles/lru.html)
