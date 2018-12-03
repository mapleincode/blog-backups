---
title: Kafka-node 的使用 Demo
date: 2018-11-01 10:37:54
tags: [Kafka, Node.js]
---



# Kafka-node

Kafka 是基于磁盘文件顺序存储而设计的类 AMQP 消息队列服务，支持集群和数据备份。有着超高的稳定性和吞吐量。

前段时间我司拟推广 Kafka，也乘机借着项目学了下 Kafka 的相关知识，虽然最后还是一直决定使用 ONS...



在 Node 环境使用量比较高的是 `Kafka-node`这个包，是一个搜狐的大佬写的。

因为大佬忙活着别的原因，文档和 Demo 对于很多细节没有补充，所以因此补充下。

## Lower Level 和 High Level

一开始在没有怎么对 Kafka 的了解的情况下，一直分不清楚两者的关系。后来在一大佬的文章中得到了解释:

> 很多时候，客户程序只是希望从Kafka读取数据，不太关心消息offset的处理。同时也希望提供一些语义，例如同一条消息只被某一个Consumer消费（单播）或被所有Consumer消费（广播）。因此，Kafka Hight Level Consumer提供了一个从Kafka消费数据的高层抽象，从而屏蔽掉其中的细节并提供丰富的语义。

> 使用Low Level Consumer (Simple Consumer)的主要原因是，用户希望比Consumer Group更好的控制数据的消费。比如：
>
> - 同一条消息读多次
> - 只读取某个Topic的部分Partition
> - 管理事务，从而确保每条消息被处理一次，且仅被处理一次
>
> 　　与Consumer Group相比，Low Level Consumer要求用户做大量的额外工作。
>
> - 必须在应用程序中跟踪offset，从而确定下一条应该消费哪条消息
> - 应用程序需要通过程序获知每个Partition的Leader是谁
> - 必须处理Leader的变化
>
> 　　使用Low Level Consumer的一般流程如下
>
> - 查找到一个“活着”的Broker，并且找出每个Partition的Leader
> - 找出每个Partition的Follower
> - 定义好请求，该请求应该能描述应用程序需要哪些数据
> - Fetch数据
> - 识别Leader的变化，并对之作出必要的响应

FROM: [Kafka设计解析（四）- Kafka Consumer设计解析](http://www.jasongj.com/2015/08/09/KafkaColumn4/)



## client

Kafka-node 支持两种 client:

1. KafkaNode.Client
2. KafkaNode.KafkaClient

前者是直接连接  zookeeper，后者是直接连接 broker。



**连接 broker**

```javascript
const client = new kafka.KafkaClient({kafkaHost: '10.3.100.196:9092'});
```

**连接 zookeeper**

```javascript
const client = new Kafka.Client('localhost:2181', clientId);
```

在目前版本(version: 3.0.1)两者暂时都能使用，都可以作为 client。

## Producer

Producer 相对比较简单:

```javascript
const kafka = require('kafka');
const Producer = kafka.Producer;
const client = new Client();
producer = new Producer(client);

function sendMsg(callback) {
    producer.send([
    {
   		topic: 'topicName',
   		messages: ['message body'], // multi messages should be a array, single message can be just a string or a KeyedMessage instance
   		key: 'theKey', // string or buffer, only needed when using keyed partitioner
   		partition: 0, // default 0
   		attributes: 2, // default: 0
   		timestamp: Date.now() // <-- defaults to Date.now() (only available with kafka v0.10 and KafkaClient only)
	}
], callback);
}

producer.on('ready', function() {
    sendMsg(function(err, data) {
        if(err) {
            console.log(err);
        }
        console.log(data);
    });
});
```

除了普通的字符串 Message，kafka-node 还支持简单的序列化消息。

```javascript
const kafka = require('kafka-node');
const KeyedMessage = kafka.KeyedMessage;
const km = new KeyedMessage('key', 'message'); // 序列化消息
      
	producer.send([
		topic: 'topicName',
         messages: [km]
    ], callback);
```


