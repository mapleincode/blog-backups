---
title: Kafka auto.offset.reset 值
date: 2018-10-31 16:13:29
tags: [ Kafka ]
---



首先附上别人的文章：

**earliest** 
当各分区下有已提交的 offset 时，从提交的 offset 开始消费；
无提交的 offset 时，从头开始消费 

**latest**
当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据

**none**
topic各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的 offset，则抛出异常

---------------------
> 作者：lishuangzhe7047 
>
> 来源：CSDN 
>
> 原文：https://blog.csdn.net/lishuangzhe7047/article/details/74530417 



主要区别是 ealiest ，是为了保证在在服务启动后，除了已经消费的消息，其他可以消费的消息都需要被消费。

一般用于旧消息处理。

而 latest 和 ealiest 不同的地方是，latest 不关心在服务器启动前的旧消息，而是着重于服务启动之后新 push 的消息。



也就是对于一个服务，如果这个 Topic 的旧消息也要做处理，则选择 esliest;

而如果你不关心在服务启动前的旧数据，而是关心从服务启动之后的旧数据，那就选择 latest，旧的消息会被过滤掉。



## 其他

在`kafka-node`这个包中，采用的配置为 `options.fromOffset`，默认 `latest`。

