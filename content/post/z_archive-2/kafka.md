---
title: kafka
author: "-"
date: 2016-07-26T13:24:33+00:00
url: kafka
categories:
  - Kafka
tags:
  - reprint
  - kafka
---
## kafka

kafka 大量使用页缓存, 经过良好调优的 kafka 集群, 磁盘读操作很少, 因为大部分消息读取操作会直接命中缓存

kafka 高吞吐量

- 大量使用操作系统页缓存，内存操作速度快且命中率高。
- Kafka 不直接参与物理I/O操作，而是交由最擅长此事的操作系统来完成。
- 采用追加写入方式，摒弃了缓慢的磁盘随机读/写操作。
- 使用以 sendfile为代表的零拷贝技术加强网络间的数据传输效率。

摘自：《Apache Kafka实战》 — 胡夕
在豆瓣阅读书店查看：[https://read.douban.com/ebook/59895902/](https://read.douban.com/ebook/59895902/)
本作品由电子工业出版社授权豆瓣阅读中国大陆范围内电子版制作与发行。
© 版权所有，侵权必究。

## 伸缩性，scalability

伸缩性表示向分布式系统中增加额外的计算资源（比如CPU、内存、存储或带宽）时吞吐量提升的能力。

## partition

Kafka的partition是不可修改的有序消息序列，也可以说是有序的消息日志。

## replica

副本（replica）

follower replica 是不能提供服务给客户端的，也就是说不负责响应客户端发来的消息写入和消息消费请求。它只是被动地向领导者副本（leader replica）获取数据

ISR的全称是in-sync replica，翻译过来就是与leader replica保持同步的replica集合

正常情况下，partition的所有replica（含leader replica）都应该与leader replica保持同步，即所有 replica都在 ISR中。因为各种各样的原因，一小部分 replica开始落后于 leader replica的进度。当滞后到一定程度时，Kafka会将这些 replica“踢”出 ISR。相反地，当这些 replica重新“追上”了 leader的进度时，那么 Kafka会将它们加回到 ISR中。这一切都是自动维护的，不需要用户进行人工干预，因而在保证了消息交付语义的同时还简化了用户的操作成本。

摘自：《Apache Kafka实战》 — 胡夕
在豆瓣阅读书店查看：[https://read.douban.com/ebook/59895902/](https://read.douban.com/ebook/59895902/)
本作品由电子工业出版社授权豆瓣阅读中国大陆范围内电子版制作与发行。
© 版权所有，侵权必究。

Event Sourcing实际上是领域驱动设计（Domain-Driven Design，DDD）的名词，它使用事件序列来表示状态变更，这种思想和 Kafka 的设计特性不谋而合。

数据倾斜（skewed）

---

分布式消息队列是是大型分布式系统不可缺少的中间件,主要解决应用耦合、异步消息、流量削锋等问题。实现高性能、高可用、可伸缩和最终一致性架构。

为何使用消息系统

### 解耦

在项目启动之初来预测将来项目会碰到什么需求,是极其困难的。消息系统在处理过程中间插入了一个隐含的、基于数据的接口层,两边的处理过程都要实现这一接口。这允许你独立的扩展或修改两边的处理过程,只要确保它们遵守同样的接口约束。

### 冗余

有些情况下,处理数据的过程会失败。除非数据被持久化,否则将造成丢失。消息队列把数据进行持久化直到它们已经被完全处理,通过这一方式规避了数据丢失风险。许多消息队列所采用的"插入-获取-删除"范式中,在把一个消息从队列中删除之前,需要你的处理系统明确的指出该消息已经被处理完毕,从而确保你的数据被安全的保存直到你使用完毕。

### 扩展性

因为消息队列解耦了你的处理过程,所以增大消息入队和处理的频率是很容易的,只要另外增加处理过程即可。不需要改变代码、不需要调节参数。扩展就像调大电力按钮一样简单。

### 灵活性 & 峰值处理能力

在访问量剧增的情况下,应用仍然需要继续发挥作用,但是这样的突发流量并不常见；如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力,而不会因为突发的超负荷的请求而完全崩溃。

### 可恢复性

系统的一部分组件失效时,不会影响到整个系统。消息队列降低了进程间的耦合度,所以即使一个处理消息的进程挂掉,加入队列中的消息仍然可以在系统恢复后被处理。

### 顺序保证

在大多使用场景下,数据处理的顺序都很重要。大部分消息队列本来就是排序的,并且能保证数据会按照特定的顺序来处理。Kafka保证一个Partition内的消息的有序性。

### 缓冲

在任何重要的系统中,都会有需要不同的处理时间的元素。例如,加载一张图片比应用过滤器花费更少的时间。消息队列通过一个缓冲层来帮助任务最高效率的执行———写入队列的处理会尽可能的快速。该缓冲有助于控制和优化数据流经过系统的速度。

### 异步通信

很多时候,用户不想也不需要立即处理消息。消息队列提供了异步处理机制,允许用户把一个消息放入队列,但并不立即处理它。想向队列中放入多少消息就放多少,然后在需要的时候再去处理它们。

Kafka是由LinkedIn开发的用于日志处理的一个分布式的消息系统,使用Scala编写. 是一种高吞吐量的分布式发布订阅消息系统
  
linkedin的日志数据容量大,但对可靠性要求不高,其日志数据主要包括用户行为 (登录、浏览、点击、分享、喜欢) 以及系统运行日志 (CPU、内存、磁盘、网络、系统及进程状态)

当前很多的消息队列服务提供可靠交付保证,并默认是即时消费 (不适合离线) 。
  
高可靠交付对linkedin的日志不是必须的,故可通过降低可靠性来提高性能,同时通过构建分布式的集群,允许消息在系统中累积,使得kafka同时支持离线和在线日志处理

Kafka架构
  
Terminology

### Broker

Kafka 集群包含一个或多个服务器,服务器节点称为broker。

broker存储topic的数据。如果某topic有N个partition,集群有N个broker,那么每个broker存储该topic的一个partition。

如果某topic有N个partition,集群有(N+M)个broker,那么其中有N个broker存储该topic的一个partition,剩下的M个broker不存储该topic的partition数据。

如果某topic有N个partition,集群中broker数目少于N个,那么一个broker存储该topic的一个或多个partition。在实际生产环境中,尽量避免这种情况的发生,这种情况容易导致Kafka集群数据不均衡。

### Topic
  
每条发布到Kafka集群的消息都有一个类别,这个类别被称为Topic。 (物理上不同Topic的消息分开存储,逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处)

### Partition

Parition 是物理上的概念,每个Topic包含一个或多个Partition.

### Producer

负责发布消息到Kafka broker

### Consumer

消息消费者, 向Kafka broker读取消息的客户端。

### Consumer Group

每个Consumer 属于一个特定的 Consumer Group (可为每个Consumer指定group name,若不指定group name则属于默认的group) 。
消息系统有两类, 一是广播, 二是订阅发布。广播是把消息发送给所有的消费者；发布订阅是把消息只发送给订阅者。Kafka通过Consumer Group组合实现了这两种机制:  实现一个topic消息广播 (发给所有的consumer) 和单播 (发给任意一个consumer) 。一个topic可以有多个Consumer Group.

### Leader

每个partition 有多个副本,其中有且仅有一个作为Leader,Leader是当前负责数据的读写的partition。

### Follower

Follower跟随Leader,所有写请求都通过Leader路由,数据变更会广播给所有Follower,Follower与Leader保持数据同步。如果Leader失效,则从Follower中选举出一个新的Leader。当Follower与Leader挂掉、卡住或者同步太慢,leader会把这个follower从“in sync replicas” (ISR) 列表中删除,重新创建一个Follower。

Kafka delivery guarantee

有这么几种可能的 delivery guarantee:

- At most once 消息可能会丢,但绝不会重复传输
- At least one 消息绝不会丢,但可能会重复传输
- Exactly once 每条消息肯定会被传输一次且仅传输一次,很多时候这是用户所想要的。

当Producer向 broker发送消息时,一旦这条消息被commit,因为replication的存在,它就不会丢。但是如果Producer发送数据给broker后,遇到网络问题而造成通信中断,那Producer就无法判断该条消息是否已经commit。虽然Kafka无法确定网络故障期间发生了什么,但是Producer可以生成一种类似于主键的东西,发生故障时幂等性的重试多次,这样就做到了Exactly once。截止到目前(Kafka 0.8.2版本,2015-03-04),这一Feature还并未实现,有希望在Kafka未来的版本中实现。 (所以目前默认情况下一条消息从Producer到broker是确保了At least once,可通过设置Producer异步发送实现At most once) 。

接下来讨论的是消息从broker到Consumer的delivery guarantee语义。 (仅针对Kafka consumer high level API) 。Consumer在从broker读取消息后,可以选择commit,该操作会在Zookeeper中保存该Consumer在该Partition中读取的消息的offset。该Consumer下一次再读该Partition时会从下一条开始读取。如未commit,下一次读取的开始位置会跟上一次commit之后的开始位置相同。当然可以将Consumer设置为autocommit,即Consumer一旦读到数据立即自动commit。如果只讨论这一读取消息的过程,那Kafka是确保了Exactly once。但实际使用中应用程序并非在Consumer读取完数据就结束了,而是要进行进一步处理,而数据处理与commit的顺序在很大程度上决定了消息从broker和consumer的delivery guarantee semantic。

读完消息先commit再处理消息。这种模式下,如果Consumer在commit后还没来得及处理消息就crash了,下次重新开始工作后就无法读到刚刚已提交而未处理的消息,这就对应于At most once

读完消息先处理再commit。这种模式下,如果在处理完消息之后commit之前Consumer crash了,下次重新开始工作时还会处理刚刚未commit的消息,实际上该消息已经被处理过了。这就对应于At least once。在很多使用场景下,消息都有一个主键,所以消息的处理往往具有幂等性,即多次处理这一条消息跟只处理一次是等效的,那就可以认为是Exactly once。 (笔者认为这种说法比较牵强,毕竟它不是Kafka本身提供的机制,主键本身也并不能完全保证操作的幂等性。而且实际上我们说delivery guarantee 语义是讨论被处理多少次,而非处理结果怎样,因为处理方式多种多样,我们不应该把处理过程的特性——如是否幂等性,当成Kafka本身的Feature)

如果一定要做到Exactly once,就需要协调offset和实际操作的输出。精典的做法是引入两阶段提交。如果能让offset和操作输入存在同一个地方,会更简洁和通用。这种方式可能更好,因为许多输出系统可能不支持两阶段提交。比如,Consumer拿到数据后可能把数据放到HDFS,如果把最新的offset和数据本身一起写到HDFS,那就可以保证数据的输出和offset的更新要么都完成,要么都不完成,间接实现Exactly once。 (目前就high level API而言,offset是存于Zookeeper中的,无法存于HDFS,而low level API的offset是由自己去维护的,可以将之存于HDFS中)

总之,Kafka默认保证At least once,并且允许通过设置Producer异步提交来实现At most once。而Exactly once要求与外部存储系统协作,幸运的是Kafka提供的offset可以非常直接非常容易得使用这种方式。

为何需要Leader Election

注意: 本文所述Leader Election主要指Replica之间的Leader Election。

引入Replication之后,同一个Partition可能会有多个Replica,而这时需要在这些Replication之间选出一个Leader,Producer和Consumer只与这个Leader交互,其它Replica作为Follower从Leader中复制数据。

因为需要保证同一个Partition的多个Replica之间的数据一致性 (其中一个宕机后其它Replica必须要能继续服务并且即不能造成数据重复也不能造成数据丢失) 。如果没有一个Leader,所有Replica都可同时读/写数据,那就需要保证多个Replica之间互相 (N×N条通路) 同步数据,数据的一致性和有序性非常难保证,大大增加了Replication实现的复杂性,同时也增加了出现异常的几率。而引入Leader后,只有Leader负责数据读写,Follower只向Leader顺序Fetch数据 (N条通路) ,系统更加简单且高效。

Propagate消息
  
Producer在发布消息到某个Partition时,先通过ZooKeeper找到该Partition的Leader,然后无论该Topic的Replication Factor为多少 (也即该Partition有多少个Replica) ,Producer只将该消息发送到该Partition的Leader。Leader会将该消息写入其本地Log。每个Follower都从Leader pull数据。这种方式上,Follower存储的数据顺序与Leader保持一致。Follower在收到该消息并写入其Log后,向Leader发送ACK。一旦Leader收到了ISR中的所有Replica的ACK,该消息就被认为已经commit了,Leader将增加HW并且向Producer发送ACK。

为了提高性能,每个Follower在接收到数据后就立马向Leader发送ACK,而非等到数据写入Log中。因此,对于已经commit的消息,Kafka只能保证它被存于多个Replica的内存中,而不能保证它们被持久化到磁盘中,也就不能完全保证异常发生后该条消息一定能被Consumer消费。但考虑到这种场景非常少见,可以认为这种方式在性能和数据持久化上做了一个比较好的平衡。在将来的版本中,Kafka会考虑提供更高的持久性。

Consumer读消息也是从Leader读取,只有被commit过的消息 (offset低于HW的消息) 才会暴露给Consumer。

### kraft

[https://developer.confluent.io/learn/kraft/](https://developer.confluent.io/learn/kraft/)
[https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum](https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum)
[https://issues.apache.org/jira/browse/KAFKA-9119](https://issues.apache.org/jira/browse/KAFKA-9119)

[http://kafka.apache.org](http://kafka.apache.org)
  
[http://www.infoq.com/cn/articles/kafka-analysis-part-1](http://www.infoq.com/cn/articles/kafka-analysis-part-1)
  
[http://www.infoq.com/cn/articles/kafka-analysis-part-2](http://www.infoq.com/cn/articles/kafka-analysis-part-2)
  
[http://www.jasongj.com/2015/01/02/Kafka%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90/](http://www.jasongj.com/2015/01/02/Kafka%E6%B7%B1%E5%BA%A6%E8%A7%A3%E6%9E%90/)

### kafka 生产环境规划

Kafka 这种应用必然需要大量地通过网络与磁盘进行数据传输，而大部分这样的操作都是通过 Java 的FileChannel.transferTo方法实现的。在Linux平台上该方法底层会调用sendfile系统调用，即采用了Linux提供的零拷贝（Zero Copy）技术。

对于预算有限且追求高性价比的公司而言，机械硬盘完全可以胜任Kafka存储的任务。 kafka 是顺序写磁盘的, ssd 避免了机械盘的寻道时间, 对于 kafka 来说不是特别重要.

[https://juejin.cn/post/6844903713916583944](https://juejin.cn/post/6844903713916583944)
[https://juejin.cn/post/6844903700616445960](https://juejin.cn/post/6844903700616445960)

kafka 实战 - 胡夕

## How to avoid rebalances and disconnections in Kafka consumers

https://developers.redhat.com/articles/2023/12/01/how-avoid-rebalances-and-disconnections-kafka-consumers