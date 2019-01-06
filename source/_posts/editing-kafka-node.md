---
title: Kafka-node 的使用 Demo
date: 2018-11-01 10:37:54
tags: [Kafka, Node.js]
---



# Kafka-node

Kafka 是基于磁盘文件顺序存储而设计的类 AMQP 消息队列服务，支持集群和数据备份。有着超高的稳定性和吞吐量。

前段时间我司拟推广 Kafka，也乘机借着项目学了下 Kafka 的相关知识，虽然最后还是一直决定使用 ONS...



在 Node 环境使用量比较高的是 `Kafka-node`这个包。所以本文主要讲诉这个包的一些简单用例。

## 关于 `Lower Level Consumer` 和 `High Level Consumer` 的区别

一开始在不怎么了解的情况下，一直分不清楚两者的关系。后来在一大佬的文章中得到了解释:

> 很多时候，客户程序只是希望从Kafka读取数据，不太关心消息 offset 的处理。同时也希望提供一些语义，例如同一条消息只被某一个 Consumer 消费（单播）或被所有 Consumer 消费（广播）。因此，Kafka Hight Level Consumer 提供了一个从 Kafka 消费数据的高层抽象，从而屏蔽掉其中的细节并提供丰富的语义。

> 而使用 Low Level Consumer (Simple Consumer)的主要原因是，用户希望比Consumer Group 更好的控制数据的消费。比如：
>
> - 同一条消息读多次
> - 只读取某个 Topic 的部分 Partition
> - 管理事务，从而确保每条消息被处理一次，且仅被处理一次
>

---

>   与 Consumer Group 相比，Low Level Consumer 要求用户做大量的额外工作。
>
> - 必须在应用程序中跟踪 offset，从而确定下一条应该消费哪条消息
>
> - 应用程序需要通过程序获知每个 Partition 的 Leader 是谁
>
> - 必须处理 Leader 的变化
>
> 　　使用 Low Level Consumer 的一般流程如下
>
> - 查找到一个“活着”的 Broker，并且找出每个 Partition 的Leader
> - 找出每个 Partition 的 Follower
> - 定义好请求，该请求应该能描述应用程序需要哪些数据
> - Fetch 数据
> - 识别 Leader 的变化，并对之作出必要的响应

参考: [Kafka设计解析（四）- Kafka Consumer设计解析](http://www.jasongj.com/2015/08/09/KafkaColumn4/)

## client

Kafka-node 支持两种 client:

1. KafkaNode.Client
2. KafkaNode.KafkaClient

前者是直接连接  zookeeper，后者是直接连接 broker。

**连接 broker**

```javascript
const client = new kafka.KafkaClient({kafkaHost: 'localhost:9092'});
```

**连接 zookeeper**

```javascript
const client = new Kafka.Client('localhost:2181', clientId);
```



Kafaka-Node 作者推荐，如果是 LowLevelConsumer 建议用 broker 的 client。如果是 HighLevelConsumer 只能连接  zookeeper 的 client。

## Producer

Producer 相对比较简单:

```javascript
'use strict';

const kafka = require('kafka-node');
const Producer = kafka.Producer;
const client = new kafka.KafkaClient({kafkaHost: 'localhost:9092'});
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

除了普通的字符串 Message，kafka-node 还支持序列化消息。

```javascript
const kafka = require('kafka-node');
const KeyedMessage = kafka.KeyedMessage;
const km = new KeyedMessage('key', 'message'); // 序列化消息
      
	producer.send([
        {
			topic: 'topicName',
        	messages: [km]
        }
    ], callback);
```



## High Level Producer

```js
'use strict';

const kafka = require('kafka-node');
const HighLevelProducer = kafka.HighLevelProducer;
const Client = kafka.Client;
const client = new Client();
const topic = 'topic1';
const producer = new HighLevelProducer(client);

producer.on('ready', function () {
  setInterval(send, 1000);
});

producer.on('error', function (err) {
  console.log('error', err);
});

function send() {
  const message = new Date().toString();
  producer.send([
    { topic: topic, messages: [ message] }
  ], function (err, data) {
      if (err) {
        console.error(err);
      } else {
        console.log(data);
      }
  });
}
```

和 LowLeverProducer 不同，不需要指定  partition。参数也相对简单。



## Consumer

Low Lever Consumer

```javascript
'use strict';

const kafka = require('kafka-node');
const Consumer = kafka.Consumer;
const Offset = kafka.Offset;
const Client = kafka.KafkaClient;

const client = new Client('localhost:9092');

const topics = [
  { topic: topic, partition: 1 },
  { topic: topic, partition: 0 }
];

const options = {
  autoCommit: false,
  fetchMaxWaitMs: 1000,
  fetchMaxBytes: 1024 * 1024
};

const consumer = new Consumer(client, topics, options);
const offset = new Offset(client);

consumer.on('message', function (message) {
  console.log(message);
});

consumer.on('error', function (err) {
  console.log('error', err);
});

/*
* If consumer get `offsetOutOfRange` event, fetch data from the smallest(oldest) offset
*/
consumer.on('offsetOutOfRange', function (topic) {
  topic.maxNum = 2;
  offset.fetch([ topic ], function (err, offsets) {
    if (err) {
      return console.error(err);
    }
    var min = Math.min.apply(null, offsets[topic.topic][topic.partition]);
    consumer.setOffset(topic.topic, topic.partition, min);
  });
});
```

