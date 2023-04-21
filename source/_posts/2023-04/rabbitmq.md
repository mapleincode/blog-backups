---
title: rabbtmq 笔记 & demo
date: 2023-04-11 18:23:33
tags: [rabbitmq, typescript]
---



## 基本

首先熟悉几个概念：

- exchange
  - 交换器，可以认为是消息 publish 的目的地。
  - 所有消息都是推送到 exchange 的
- queue
  - 队列，consume 监听的位置
  - queue 其实和 publish 没有任何关系
  - 只是为了方便多个消费者消费同一个消息
- **流程**
  1. **创建 exchange，也可以复用已存在的 exchange。**
     - **如果 exchange 已存在则不创建**
     - **如果已存在的 exchange 的属性与代码配置的不符合则报错**
  2. **consumer 创建 queue**
     - **如果已经存在则改为获取 queue，参数以存在的为准，如果链接的 queue 参数不符合就报错**
  3. **consummer 绑定 queue 和 exchange**
     - **这一步以前我理解为 publisher 也需要，其实 publisher 根本不需要 queue，更不需要绑定 queue**
  4. **publisher 推送消息到 exchange**
     - **参数包括**
       - **消息本体**
       - **routingKey (除了 fanout 模式和 header 模式)**
     - publisher 不需要 queue！
  5. **exchange 根据规则把对应的消息放入 queue**
  6. **consumer 获取 queue 的消息并消费**
- fanout
  - 任何到达 exchange 的消息都会被放到绑定的 queue 去
  - 可以使用独占模式
- direct
  - 路由模式的一种。
  - consume 和 publish 都必须传入 routingKey （路由 key）
  - consume 可以绑定多个 routingKey
- topic
  - 路由模式的一种，direct 的扩展
  - routingKey 支持 * 和 # 匹配
    - *代表匹配任意一个关键词，#代表匹配一个或多个关键词
    - 关键字之间通过 . 连接
- header
  - 据说这玩意儿不行就懒得研究了，本质也是使用 header 替换 routingKey



```typescript
/**
 * @Author: maple
 * @Date: 2022-05-26 11:14:52
 * @LastEditors: maple
 * @LastEditTime: 2023-04-10 14:24:48
 */
import { Options, connect, Message, Connection, Channel } from 'amqplib'

type MessageRaw = string|object|Buffer;

export enum QueueType {
  direct = 'direct',
  topic = 'topic',
  headers = 'headers',
  fanout = 'fanout',
  match = 'match'
}

type CreateChannelOptions = {
  queue?: string;
  type?: QueueType;
  exchange?: string,
  options?: CreateChannelOptionsExtra;
  publisher: boolean
}

type CreateChannelOptionsExtra = {
  routingKey?: string|string[];
  exchangeOptions?: ExchangeOptions,
  queueOptions?: QueueOptions
}

type QueueOptions = {
  durable?: boolean; // 是否持久化， 默认 false
  exclusive?: boolean; // 是否独占， 默认 false
} & Options.AssertQueue

type ExchangeOptions = {
  durable?: boolean; // 是否持久化，默认 false
} & Options.AssertExchange;

type SendToQueueOptions = {
  persistent?: boolean; // 是否持久化;默认 false;
  queue: string // 消息队列名
} & Options.Publish;

type PublishOptions = {
  routingKey?: string[]|string;
  exchange?: string;
} & Options.Publish;

interface Consumer {
  (content: string, msg: Message): void;
}

type ConsumeOptions = {
  noAck?: boolean;
  catchErr?: boolean;
} & Options.Consume;

interface RabbitMQChannelOptions {
  type: QueueType;
  conn: Connection;
  channel: Channel;
  queue: string;
  exchange?: string;
  routingKeys?: string[];
  queueOptions?: QueueOptions;
  exchangeOptions?: ExchangeOptions;

  prefetch?: number;
}

export class RabbitMQChannel {
  private readonly type: QueueType;
  private readonly conn: Connection;
  private readonly channel: Channel;
  private readonly queue: string;
  private readonly exchange?: string;
  private readonly routingKeys?: string[];
  private queueOptions?: QueueOptions;
  private exchangeOptions?: ExchangeOptions;
  private readonly prefetchCount?: number;
  constructor(options: RabbitMQChannelOptions) {

    this.type = options.type;
    this.conn = options.conn;
    this.channel = options.channel;
    this.queue = options.queue;
    this.exchange = options.exchange;
    this.queueOptions = options.queueOptions;
    this.exchangeOptions = options.exchangeOptions;
    this.routingKeys = options.routingKeys;
    this.prefetchCount = options.prefetch;

    if (!this.type || !this.conn || !this.channel) {
      throw new Error('illegal params');
    }

    /**
     * 设置 prefetch 参数，避免大量消息堆集
     * prefetch允许为每个consumer指定最大的 unacked messages数目。
     * 简单来说就是用来指定一个consumer一次可以从Rabbit中获取多少条message并缓存在client中(RabbitMQ提供的各种语言的client library)。
     * 一旦缓冲区满了，Rabbit将会停止投递新的message到该consumer中直到它发出ack。
     * 链接：https://www.jianshu.com/p/4d043d3045ca
     */
    if (typeof this.prefetchCount === 'number' && this.prefetchCount > 0) {
      this.prefetch(this.prefetchCount)
          .catch(err => {
            console.error(err)
          });
    }
  }

  /**
   * 将任意类型的参数转换为 buffer
   * @returns {object} buffer
   * @param msg
   */
  _handleMsgToBuffer (msg: MessageRaw): Buffer {
    let buffer: Buffer;

    if (typeof msg === 'object' && !Buffer.isBuffer(msg)) {
      const hasToString = msg.toString && msg.toString !== Object.prototype.toString && !Array.isArray(msg);
      if (hasToString) {
        msg = msg.toString();
      } else {
        msg = JSON.stringify(msg);
      }
    }

    if (!Buffer.isBuffer(msg)) {
      buffer = Buffer.from(msg || '');
    } else {
      buffer = msg;
    }
    return buffer;
  }

  /**
   * send to queue
   *
   * 发送数据到队列
   * @param {object} msg 数据
   * @param {Object} sendOptions send to queue 参数
   */
  sendToQueue (msg: MessageRaw, sendOptions?: SendToQueueOptions) {
    const queue = sendOptions?.queue;
    const endQueue = queue || this.queue || "";

    this.channel.sendToQueue(endQueue, this._handleMsgToBuffer(msg), sendOptions);
  }

  /**
   * sendToQueue 的复杂版本，可以传递 routingKey 和 exchange
   * routingKey 和 exchange 最后不能为空
   * @param {*} msg 消息
   * @param {} publishOptions 推送消息参数
   */
  publish (msg: MessageRaw, publishOptions?: PublishOptions) {
    let routingKey = publishOptions?.routingKey;
    let exchange = publishOptions?.exchange;

    let routingKeys: string[]|undefined = this.routingKeys;

    if (routingKey) {
      routingKeys = Array.isArray(routingKey) ? routingKeys : [routingKey];
    }

    if (routingKeys == undefined || !routingKeys.length || routingKeys[0] === undefined) {
      routingKeys = [""];
    }

    exchange = exchange || this.exchange;

    if (!exchange) {
      throw new Error('需要额外指定 exchange');
    }

    for(const routingKey of routingKeys) {
      const success = this.channel.publish(exchange, routingKey, this._handleMsgToBuffer(msg), publishOptions);
    }
  }

  async consume (consumer: Consumer, consumeOptions?: ConsumeOptions) {

    const noAck = consumeOptions?.noAck;
    const catchErr = consumeOptions?.catchErr || false;

    await this.channel.consume(this.queue, async (msg) => {
      if (!msg) {
        return;
      }

      try {
        // buffer msg
        const msgContent = msg.content.toString();

        // consumer handle
        await consumer(msgContent, msg);
      } catch (err: any) {
        if (catchErr) {
          throw err;
        }
      }


      // 自动 ack
      if (!noAck) {
        // ack msg
        this.channel.ack(msg);
      }
    }, consumeOptions);
  }

  async ackAll () {
    // await this._handleAssertChannel();
    await this.channel.ackAll();
  }

  async nack (message: Message, allUpto: boolean, requeue: boolean) {
    // await this._handleAssertChannel();
    this.channel.nack(message, allUpto, requeue);
  }

  async nackAll (requeue: boolean) {
    // await this._handleAssertChannel();
    this.channel.nackAll(requeue);
  }

  async prefetch (number: number) {
    if ( number >= 1) {
      await this.channel.prefetch(number);
    }
  }

  getChannel(): Channel {
    return this.channel;
  }
}

export class RabbitMQ {
  private link: Options.Connect | string | undefined;
  private conn: Connection | undefined;

  constructor (link: Options.Connect|String) {
    this.setConnect(link);
  }

  setConnect(link: Options.Connect|String) {
    if (!link) {
      link = 'amqp://localhost'
    }
    const defaultLinkOptions =
        {
          protocol: 'amqp',
          hostname: 'localhost',
          port: 5672,
          username: 'guest',
          password: 'guest',
          locale: 'en_US',
          frameMax: 0,
          heartbeat: 0,
          vhost: '/'
        };

    this.link = typeof link === 'string'
        ? link
        : Object.assign(defaultLinkOptions, link || {});
  }

  /**
   * 连接并返回 connect 对象
   * @param {string|object} link amqp 连接参数
   * @returns connect
   */
  async connet (link?: Options.Connect|string): Promise<Connection> {
    const realLink = link || this.link;

    if (!realLink) {
      throw new Error('Link can be null');
    }

    this.conn = await connect(realLink);
    return this.conn;
  }

  // fanout 绑定 queue 广播 (不需要绑定 RoutingKey 和 BindingKey)
  // direct 绑定 queue 发送 (绑定 RoutingKey 和 BindingKey) (如果交换器只有一个队列，则 routingKey 和 BindingKey 只有一个)
  // -- BindingKey(queue) 用于队列和交换器绑定
  // -- RoutingKey 如果不绑定，发送给所有队列，绑定了只发给符合的队列

  // headers
  // match
  // topic  绑定 queue 和 topic

  // 'direct' | 'topic' | 'headers' | 'fanout' | 'match'
  async createChannel (createChannelOptions?: CreateChannelOptions): Promise<RabbitMQChannel> {
    let queue = createChannelOptions?.queue; // 队列名称
    const type = createChannelOptions?.type || QueueType.direct; // exchange 类型
    const exchange = createChannelOptions?.exchange; // 不传就是默认的 exchange
    const options = createChannelOptions?.options;



    let queueOptions = options?.queueOptions || {};
    const exchangeOptions = options?.exchangeOptions;
    const routingKey = options?.routingKey;

    // 独占模式
    if (!queue || queue.trim() === '') {
      queue = '';
      queueOptions.exclusive = true;
    }

    // 判断是否为 publisher，如果是 publisher 就必须传入 routingKey
    // direct, topic, match
    if (createChannelOptions?.publisher === false) {
      // consumer
      if (routingKey == undefined && (
          QueueType.direct === type ||
          QueueType.topic === type ||
          QueueType.match === type)) {
        throw new Error("direct 必传 routingKey");
      }
    }


    // routingKey 可能是列表
    const routingKeys = typeof routingKey === 'string' ? [routingKey] : routingKey || [];

    if (!this.conn) {
      throw new Error('conn should connect');
    }

    // 创建 channel
    const channel = await this.conn.createChannel();

    if (exchange) {
      // 申明 exchange
      await channel.assertExchange(exchange, type, exchangeOptions);
    }

    if (!createChannelOptions?.publisher) {
      const bindExchange = exchange || '';

      // 申明 queue
      const q = await channel.assertQueue(queue, queueOptions);
      if (QueueType.fanout === type) {
        await channel.bindQueue(q.queue, bindExchange, '');
      } else {
        for(const routingKey of routingKeys) {
          // 绑定 queue / exchange / routingKey
          await channel.bindQueue(q.queue, bindExchange, routingKey);
        }
      }
    }

    // 返回 RabbitMQChannel 参数
    return new RabbitMQChannel({
      type,
      conn: this.conn,
      channel,
      queue,
      exchange,
      routingKeys: routingKeys
    });
  }

  async close () {
    if (this.conn) {
      await this.conn.close();
    }
  }
}

```

