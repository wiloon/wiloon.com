---
title: kafka command
author: wiloon
type: post
date: 2018-05-07T08:44:53+00:00
url: /?p=12205
categories:
  - Uncategorized

---
## topic

### list topic

<pre><code class="language-bash line-numbers">bin/kafka-topics.sh --list --zookeeper localhost:2181
</code></pre>

### 查看topic详细信息

<pre><code class="language-bash line-numbers">bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic topic0 --describe
# replication-factor: 副本数, partitions: 分区数
# topic名中有. 或 _ 会提示： WARNING: Due to limitations in metric names, topics with a period ('.') or underscore ('_') could collide. To avoid issues it is best to use either, but not both.
</code></pre>

### create topic

<pre><code class="language-bash line-numbers"># cloudera kafka
/opt/cloudera/parcels/KAFKA/bin/kafka-topics \
--create \
--zookeeper 127.0.0.1:2181 \
--replication-factor 3 \
--partitions 30 \
--topic topic_0 \
--config retention.ms=1296000000 \
--config retention.bytes=10737418240

#kafka
bin/kafka-topics.sh --create \
--zookeeper test-zookeeper-1,test-zookeeper-2,test-zookeeper-3 \
--replication-factor 3 \
--partitions 5 \
--topic topic0
</code></pre>

### 调整分区数

<pre><code class="language-bash line-numbers">bin/kafka-topics.sh --zookeeper ip0:2181 --alter --topic topic0 --partitions 5
</code></pre>

### 重置offset

<pre><code class="language-bash line-numbers">./kafka-consumer-groups.sh --bootstrap-server broker_ip_0:9092 \
--reset-offsets --group &lt;group id&gt; \
--topic topic0:partition0 \
--to-offset 20 \
--execute
</code></pre>

### 查看kafka版本

到kafka/libs 目录下查看kafka包的文件名，如：
  
kafka_2.10-0.8.2-beta.jar, where 2.10 is Scala version and 0.8.2-beta is Kafka version.

### config kafka server

edit config/server.properties

broker.id=0
  
listeners=PLAINTEXT://:9092
  
zookeeper.connect=localhost:2181

### producer

<pre><code class="language-bash line-numbers">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topic0
</code></pre>

### consume

<pre><code class="language-bash line-numbers">bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 \
--topic topic0 \
--from-beginning \
--property "parse.key=true" \
--property "key.separator=:"

</code></pre>

<pre><code class="language-bash line-numbers"># 删除topic
bin/kafka-topics.sh --topic t0 --delete --zookeeper test-zookeeper-1



#edit bin/kafka-server-start.sh, change memory setting KAFKA_HEAP_OPTS
#start kafka server
bin/kafka-server-start.sh config/server.properties

#start kafka server as daemon
bin/kafka-server-start.sh -daemon config/server.properties

bin/kafka-console-producer.sh --broker-list localhost:9092 --topic topic0
bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic topic0 --from-beginning --property "parse.key=true" --property "key.separator=:"
</code></pre>

<pre><code class="language-bash line-numbers">./bin/kafka-server-start.sh config/server.properties

查看不可用分区 ./kafka-topics.sh --topic test --describe --unavailable-partitions --zookeeper
bin/kafka-console-producer.sh --broker-list test-kafka-1:9092 --topic t0
bin/kafka-console-consumer.sh --bootstrap-server --zookeeper xxx:2181 test-kafka-1:9092 --topic t0 --from-beginning

bin/kafka-console-consumer.sh --bootstrap-server test-kafka-1:9092 --topic t0 --from-beginning
# 会只消费 N 条数据，如果配合 --from-beginning，就会消费最早 N 条数据。
bin/kafka-console-consumer.sh --bootstrap-server test-kafka-1:9092 --topic t0 --max-messages 10
</code></pre>

### 调整 ReplicationFactor

<pre><code class="language-bash line-numbers">cat increase-replication-factor.json

{"version":1,
"partitions":[{"topic":"connect-configs","partition":0,"replicas":[0,1,2]}]
}

bin/kafka-reassign-partitions.sh --zookeeper localhost:2182 --reassignment-json-file increase-replication-factor.json --execute

</code></pre>

### consumer

<pre><code class="language-bash line-numbers">/opt/cloudera/parcels/KAFKA/bin/kafka-console-consumer --bootstrap-server 192.168.0.1:9092,192.168.0.2:9092,192.168.0.3:9092 --topic topic0
</code></pre>

### install

download http://kafka.apache.org/downloads.html

#### install and start zookeeper

<http://blog.wiloon.com/?p=7242>

#### docker

<pre><code class="language-bash line-numbers">docker run  -d --name kafka \
-p 9092:9092 \
-e KAFKA_BROKER_ID=0 \
-e KAFKA_ZOOKEEPER_CONNECT=192.168.50.115:2181 \
-e KAFKA_ADVERTISED_HOST_NAME=192.168.50.115 \
-e KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://192.168.50.115:9092 \
-e KAFKA_LISTENERS=PLAINTEXT://0.0.0.0:9092 \
-e KAFKA_AUTO_CREATE_TOPICS_ENABLE=false \
-v kafka-data:/kafka \
-t wurstmeister/kafka
</code></pre>

### server.properties

advertised.host.name：是注册到zookeeper，client要访问的broker地址。（可能producer也是拿这个值，没有验证）

如果advertised.host.name没有设，会用host.name的值注册到zookeeper，如果host.name也没有设，则会使用JVM拿到的本机hostname注册到zk。

这里有两个坑要注意：

如果advertised.host.name没有设，host.name不能设为0.0.0.0，否则client通过zk拿到的broker地址就是0.0.0.0。
  
如果指定要bind到所有interface，host.name不设就可以。

如果host.name和advertised.host.name都不设，client通过zk拿到的就是JVM返回的本机hostname，如果这个hostname是client无法访问到的，client就会连不上broker。
  
所以如果要bind到所有interface，client又能访问，解决的办法是host.name不设或设置0.0.0.0，advertised.host.name一定要设置为一个client可以访问的地址，如直接设IP地址。
  
如果不需要bind到所有interface，也可以只在host.name设置IP地址。

简单的检查broker是否可以被client访问到的办法，就是在zookeeper中看broker信息，上面显示的hostname是否是client可以访问到的地址。
  
在zkCli中执行get /brokers/<id>

### Kafka 访问协议说明

Kafka当前支持四种协议类型的访问：PLAINTEXT、SSL、SASL\_PLAINTEXT、SASL\_SSL。

Kafka服务启动时，默认会启动PLAINTEXT和SASL\_PLAINTEXT两种协议类型的访问监听。可通过设置Kafka服务配置“ssl.mode.enable”为“true”，来启动SSL和SASL\_SSL两种协议类型的访问监听。

下表是四中协议类型的简单说明：

协议类型

说明

支持的API

默认端口

PLAINTEXT

支持无认证的明文访问

新API和旧API

21005
  
https://www.jianshu.com/p/25a7b0ceb78a
  
https://github.com/wurstmeister/kafka-docker
  
https://juejin.im/entry/5cbfe36b6fb9a032036187aa
  
https://my.oschina.net/u/218540/blog/223501
  
https://www.cnblogs.com/AcAc-t/p/kafka\_topic\_consumer\_group\_command.html
  
https://blog.csdn.net/lzufeng/article/details/81743521