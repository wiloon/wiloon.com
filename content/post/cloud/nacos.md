---
title: "nacos"
author: "-"
date: ""
url: ""
categories:
  - inbox
tags:
  - inbox
---
## "nacos"


distro协议介绍
distro协议网上的资料比较少，因为它是阿里"自创的协议"，通过源码总结一下distro协议的关键点: 

distro协议是为了注册中心而创造出的协议；

客户端与服务端有两个重要的交互，服务注册与心跳发送；

客户端以服务为维度向服务端注册，注册后每隔一段时间向服务端发送一次心跳，心跳包需要带上注册服务的全部信息，在客户端看来，服务端节点对等，所以请求的节点是随机的；

客户端请求失败则换一个节点重新发送请求；

服务端节点都存储所有数据，但每个节点只负责其中一部分服务，在接收到客户端的"写" (注册、心跳、下线等) 请求后，服务端节点判断请求的服务是否为自己负责，如果是，则处理，否则交由负责的节点处理；

每个服务端节点主动发送健康检查到其他节点，响应的节点被该节点视为健康节点；

服务端在接收到客户端的服务心跳后，如果该服务不存在，则将该心跳请求当做注册请求来处理；

服务端如果长时间未收到客户端心跳，则下线该服务；

负责的节点在接收到服务注册、服务心跳等写请求后将数据写入后即返回，后台异步地将数据同步给其他节点；

节点在收到读请求后直接从本机获取后返回，无论数据是否为最新。

https://juejin.im/post/6844904167924826119
https://developer.aliyun.com/article/723938
https://mp.weixin.qq.com/s?__biz=MzI5NjE2MDQwNg==&mid=2247483740&idx=1&sn=e1117bbe107b2e257fcf93cfe04b6218&chksm=ec49dca5db3e55b3305e955972278991e856371693d401f65b26ab055e2b236e6b12efe33753&token=169335499&lang=zh_CN&scene=21#wechat_redirect
https://juejin.im/post/6844904167924826119
https://blog.csdn.net/fly910905/article/details/100023415
https://my.oschina.net/u/867417/blog/1865971