---
title: RabbitMQ 客户端(nodejs)
tags: [nodejs, rabbitmq]
date: 2022-05-27 17:30:48
---

```javascript
class RabbitMQChannel {
  constructor (options) {
    const {
      type,
      connect,
      channel,
      queue = '',
      exchange = '',
      routingKey = '',
      queueOptions = {},
      exchangeOptions = {}
    } = options;

    this.type = type;
    this.connect = connect;
    this.channel = channel;
    this.queue = queue;
    this.options = options;
    this.exchange = exchange;
    this.queueOptions = queueOptions;
    this.exchangeOptions = exchangeOptions;
    this.routingKey = routingKey;

    // const {
    //   queueOptions = {/* durable: true */},
    //   exchangeOptions = {/* exclusive: false */}
    // } = options;

    if (!this.type || !this.connect || !this.channel || !this.queue) {
      throw new Error('illegal params');
    }

    /**
     * 设置 prefetch 参数，避免大量消息堆集
     */
    if (this.channelOptions.prefetch) {
      this.prefetch(this.channelOptions.prefetch);
    }
  }

  /**
   * 将任意类型的参数转换为 buffer
   * @param {Object|String} buffer msg 参数
   * @returns {Buffer} buffer
   */
  _handleMsgToBuffer (buffer) {
    if (typeof buffer === 'object' && !Buffer.isBuffer(buffer)) {
      const hasToString = buffer.toString && buffer.toString !== Object.prototype.toString && !Array.isArray(buffer);
      if (hasToString) {
        buffer = buffer.toString();
      } else {
        buffer = JSON.stringify(buffer);
      }
    }
    if (typeof buffer === 'string') {
      buffer = Buffer.from(buffer);
    }
    return buffer;
  }

  // async _handleAssertChannel () {
  //   if (!this.assertStatus) {
  //     await this.channel.assertQueue(this.queue, this.type, this.assertOptions);
  //     this.assertStatus = true;
  //   }
  // }
  /**
   * send to queue
   *
   * 发送数据到队列
   * @param {Buffer} buffer 数据
   * @param {Object} sendOptions send to queue 参数
   */
  sendToQueue (buffer, sendOptions = {/* persistent: false 持久 */}) {
    const { queue } = sendOptions;
    this.channel.sendToQueue(queue || this.queue, this._handleMsgToBuffer(buffer), sendOptions);
  }

  publish (buffer, publishOptions = {}) {
    let {
      routingKey,
      exchange
    } = publishOptions;

    if (Array.isArray(this.routingKey) && this.routingKey.length !== 1 && !routingKey) {
      throw new Error('需要额外指定 options.routingKey');
    } else {
      routingKey = routingKey || this.routingKey[0] || this.routingKey;
    }

    exchange = exchange || this.exchange;

    this.channel.publish(exchange || this.exchange, routingKey, this._handleMsgToBuffer(buffer), publishOptions);
  }

  async consume (consumer, consumeOptions = { /* noAck: false */}) {
    if (!this.assertStatus) {
      await this.channel.assertQueue(this.queue, this.type, this.assertOptions);
      this.assertStatus = true;
    }

    this.channel.consume(this.queue, async (msg) => {
      if (!msg) {
        return;
      }

      // buffer msg
      const msgContent = msg.content;

      // consumer handle
      await consumer(msgContent, msg);

      // 自动 ack
      if (!consumeOptions.noAck) {
        // ack msg
        this.channel.ack(msg);
      }
    }, consumeOptions);
  }

  async ackAll () {
    // await this._handleAssertChannel();
    await this.channel.ackAll();
  }

  async nack (message, allUpto, requeue) {
    // await this._handleAssertChannel();
    await this.channel.nack(message, allUpto, requeue);
  }

  async nackAll (requeue) {
    // await this._handleAssertChannel();
    await this.channel.nackAll(requeue);
  }

  prefetch (number) {
    if (typeof number === 'number' && number >= 1) {
      this.channel.prefetch(parseInt(number));
    }
  }
}

class RabbitMQ {
  constructor (link = 'amqp://localhost') {
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

  async connet (link) {
    const _link = link || this._link;
    this.connet = await amqplib.connect(_link);
    // return this;
    return this.connect();
  }

  // fanout 绑定 queue 广播
  // direct 绑定 queue 发送
  // headers
  // match
  // topic  绑定 queue 和 topic

  // 'direct' | 'topic' | 'headers' | 'fanout' | 'match'
  async createChannel (
    queue = '',
    exchange,
    type = 'direct',
    options = {
      queueOptions: {/* durable: true, exclusive: false */},
      exchangeOptions: {/* durable: false */}
    }
  ) {
    if (typeof exchange === 'string') {
      if (['direct', 'topic', 'headers', 'fanout', 'match'].indexOf(exchange) > -1) {
        // 替换 options 为 type
        if (typeof type === 'object' && !options) {
          options = type;
        }

        type = exchange;
        exchange = null;
      }
    }

    const {
      queueOptions,
      exchangeOptions,
      routingKey = ''
    } = options;

    // 创建 channel
    const channel = await this.connet.createChannel();

    if (exchange) {
      // 如果 queue 默认是 '' 就设置为独占，链接断开后会自动删除
      if (queue === '' && queueOptions.exclusive === undefined) {
        queueOptions.exclusive = true;
      }

      // 申明 exchange
      await channel.assertExchange(exchange, type, exchangeOptions);

      // 申明 queue
      const q = await channel.assertQueue(queue, type, queueOptions);

      let routingKeys = routingKey;

      if (!Array.isArray(routingKeys)) {
        routingKeys = [routingKeys || ''];
      }

      routingKeys.forEach(routingKey => {
        // 绑定 queue 和 exchange
        channel.bindQueue(q.queue, exchange, routingKey);
      });
    } else {
      await channel.assertQueue(queue, type, queueOptions);
    }

    // 返回 RabbitMQChannel 参数
    return new RabbitMQChannel(type, this.connet, channel, queue, exchange, options);
  }

  async close () {
    if (this.connet) {
      this.connet.close();
    }
  }
}
```

