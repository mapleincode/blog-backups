---
title: Kafka-node 的使用 Demo
date: 2019-01-07 03:20:39
tags: [Kafka, Node.js]
---



# Kafka-node

Kafka 是基于磁盘文件顺序存储而设计的类 AMQP 消息队列服务，支持集群和数据备份。有着超高的稳定性和吞吐量。

前段时间我司拟推广 Kafka，也乘机借着项目学了下 Kafka 的相关知识，虽然最后还是一直决定使用 ONS..

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



这里有几个要点

1. offsetOutOfRange 事件

   字面上的意思是超出实际索引值。但是查阅了网上的一些真实案例，大多数对事情发生的原因并不一致。也就是有多种可能性会导致越界。

   而 `offset.fetch` 则是获取目前 `smallest(oldest)`的 offset 进行消费(这个是作者的说法，真实情况也不了解)。

2. commit

   作为一个消息队列，需要支持的一点是支持消息失败的重复消费。

   首先说明一点是 Kafka 本身支持 commit ，但是他的做法是保存 offset。也就是消费进度。由于 kafka 的消费顺序是不可变的。所以 consumer 有且只能顺序消费，并保存最后成功的一个消息的 index 作为 offset。

   因此 kafka-node 默认是选择`autoCommit = true;`

   如果要选择自己手动 commit。

   则需要`autoCommit = false ` 和 `consumer.commit(cb)`。

   > 这里我之前有一只弄不懂的一个地方，为什么 commit 参数里没有可以选择 offset 的地方。

   一开始，我以为 commit 是这样的运行的

   ```javascript
   consumer.on('message', function (message) {
     console.log(message);
     consumer.commit(callback);
   });
   ```

   但是事实证明这个并没有效果。

   除了 `commit()` 之外，还有 `consumer.pause()` 也同样没有效果。

   作者在 `pause()`说明了一段话

   >  **\*Calling pause does not automatically stop messages from being emitted.*** 

   大概意思是如果消息正在被接受过程中是无法被暂停的。

   这个涉及 Kafka 的设计逻辑，Kafka 每次给客户端推送消息的单位并不是数量，而是大小。也就是如果消息很少，可能每次推送消息可能有几个甚至几十个。

   Kafka-node 处理成了 emit 模式，这样使得每次获取消息之后，message 会被触发多次，如果单纯在 message.on() 中写入 commit 那肯定不会奏效的。(commit  在同一个 Node 轮询中不会被触发)。

   所以正确的做法是 `process.nextTick()`。配合 `consumer.pause()`可以实现消息在客户端消费之后再继续消费的功能。

   ```javascript
   let status = true;
   consumer.on('message', function(message) {
       console.log(message);
   
       process.nextTick(function() {
           if(status) {
               status = false;
               consumer.pause(); // 暂停
               console.log('pause');
               setTimeout(() => {
                   consumer.commit(function(err, result) {
                       console.log('commit message', err, result);
                       consumer.resume();
                       status = true;
                       console.log('resume')
                   })
                   ;
               }, 10000);
           }
       })
   });
   ```






## High Level Consumer

High Level Consumer 和 Low Level Consumer 相对。(区别上文已经说明，这里给出官方的 Demo)

```javascript
var kafka = require('kafka-node');
var HighLevelConsumer = kafka.HighLevelConsumer;
var Client = kafka.Client;
var argv = require('optimist').argv;
var topic = argv.topic || 'topic1';
var client = new Client('localhost:2181');
var topics = [{ topic: topic }];
var options = { autoCommit: true, fetchMaxWaitMs: 1000, fetchMaxBytes: 1024 * 1024 };
var consumer = new HighLevelConsumer(client, topics, options);

consumer.on('message', function (message) {
  console.log(message);
});

consumer.on('error', function (err) {
  console.log('error', err);
});
```

> commit 方法和 low level consumer 相同。

最大的区别是不需要区分 partition。



## ConsumerGroup

这个算是算是最需要知道的一个 API 了。

一般情况下，我们期望可以支持分布式，并且可以自己选择 partition 来进行消费。如果每次都需要一个个手动取消费就会很繁琐。而 Kafka 本身不支持多个 consumer 消费同一个 partition。

ConsumerGroup 最大的特点，就是可以根据 client 的数量对消费情况进行自动分配。一旦某个 client 挂了，就会立刻进行重新分配，实现消息队列的分布式消费。

当然 ConsumerGroup 本身就是基于 HighLevelConsumer 封装的。



```javascript
var async = require('async');
var ConsumerGroup = require('..').ConsumerGroup;

var consumerOptions = {
  autoCommit: false,
  autoCommitIntervalMs: 1000,
  host: 'localhost:2181',
  groupId: 'ExampleTestGroup',
  sessionTimeout: 15000,
  protocol: ['roundrobin'],
  	commitOffsetsOnFirstJoin: false,
  fromOffset: 'earliest' // equivalent of auto.offset.reset valid values are 'none', 'latest', 'earliest'
};

var topics = ['RebalanceTopicNew', 'RebalanceTestNew'];

var consumerGroup = new ConsumerGroup(Object.assign({id: 'consumer1'}, consumerOptions), topics);
consumerGroup.on('error', onError);
consumerGroup.on('message', onMessage);

var consumerGroup2 = new ConsumerGroup(Object.assign({id: 'consumer2'}, consumerOptions), topics);
consumerGroup2.on('error', onError);
consumerGroup2.on('message', onMessage);
consumerGroup2.on('connect', function () {
    setTimeout(function () {
        consumerGroup2.close(true, function (error) {
  	    console.log('consumer2 closed', error);
    });
  }, 25000);
});

var consumerGroup3 = new ConsumerGroup(Object.assign({id: 'consumer3'}, consumerOptions), topics);
consumerGroup3.on('error', onError);
consumerGroup3.on('message', onMessage);

function onError (error) {
  console.error(error);
  console.error(error.stack);
}

function onMessage (message) {
  console.log('%s read msg Topic="%s" Partition=%s Offset=%d', this.client.clientId, message.topic, message.partition, message.offset);
}

process.once('SIGINT', function () {
  async.each([consumerGroup, consumerGroup2, consumerGroup3], function (consumer, callback) {
    consumer.close(true, callback);
  });
});

```



具体的效果自己去 test 一下就知道了。当 consumer2 挂了之后，剩下的两个 consumerGroup 的其中一个接手了 consumerGroup2 的任务。



> 这里比较蛋疼的是，我测试 commit 效果，但是发现每次退出之后 commit 老是被提交。后面去翻阅源码，发现 consumer.close() 会强制提交 offset。





## 其他的一系列函数

其实大部分的函数作者都描述的很详细了，例如`producer.createTopics(topics, cb)`, `consumer.addTopics(topics, cb, fromOffset)`, `consumer.removeTopics(topics, cb)`, `consumer.setOffset(topic, partition, offset)`。



## 其他的一些坑

1. 作者自己整理的一些 q & a https://www.npmjs.com/package/kafka-node#troubleshooting--faq

2. partitions配置的值过小造成错误(kafka1.0.1)

   ```
   #(java)org.apache.kafka(执行producer.send)
   Exception in thread "main" org.apache.kafka.common.KafkaException: Invalid partition given with record: 1 is not in the range [0...1).
       at org.apache.kafka.clients.producer.KafkaProducer.waitOnMetadata(KafkaProducer.java:908)
       at org.apache.kafka.clients.producer.KafkaProducer.doSend(KafkaProducer.java:778)
       at org.apache.kafka.clients.producer.KafkaProducer.send(KafkaProducer.java:768)
       at com.wenshao.dal.TestProducer.main(TestProducer.java:36)
   
   
   # (nodejs) kafka-node异常 (执行producer.send后的异常)
   { BrokerNotAvailableError: Could not find the leader
       at new BrokerNotAvailableError (D:\project\node\kafka-test\src\node_modules\kafka-node\lib\errors\BrokerNotAvailableError.js:11:9)
       at refreshMetadata.error (D:\project\node\kafka-test\src\node_modules\kafka-node\lib\kafkaClient.js:831:16)
       at D:\project\node\kafka-test\src\node_modules\kafka-node\lib\client.js:514:9
       at KafkaClient.wrappedFn (D:\project\node\kafka-test\src\node_modules\kafka-node\lib\kafkaClient.js:379:14)
       at KafkaClient.Client.handleReceivedData (D:\project\node\kafka-test\src\node_modules\kafka-node\lib\client.js:770:60)
       at Socket.<anonymous> (D:\project\node\kafka-test\src\node_modules\kafka-node\lib\kafkaClient.js:618:10)
       at Socket.emit (events.js:159:13)
       at addChunk (_stream_readable.js:265:12)
       at readableAddChunk (_stream_readable.js:252:11)
       at Socket.Readable.push (_stream_readable.js:209:10) message: 'Could not find the leader' }
   ```

   解决方法: 修改num.partitions的值，partitions在是在创建topic的时候默认创建的partitions节点的个数，只对新创建的topic生效，所有尽量在项目规划时候定一个合理的值。也可以通过命令行动态扩容()

   ```
   ./bin/kafka-topics.sh --zookeeper  localhost:2181 --alter --partitions 2 --topic  foo
   ```

   作者：yanshaowen 链接：https://www.jianshu.com/p/9e72b3942c59

   > 这个问题，作者提出来的的办法是:
   >
   > Call `client.refreshMetadata()` before sending the first message.
   >
   > 我觉得作者是想解决的是另一种问题吧。
   >
   > 我是自己搭建的 kafka 服务，所以也不懂怎么管理，修改了 partitions 配置之后，报错就没了。