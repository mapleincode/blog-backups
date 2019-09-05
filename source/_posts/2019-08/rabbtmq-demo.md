---
title: RabbitMQ Node demo
date: 2019-09-05 15:19:00
tags: [RabbitMQ]
---



```js
const options = {
    hostname: '192.168.2.121',
    username: 'username',
    password: 'password'
};


class Rabbit {
    constructor(options) {
        this._options = Object.assign({
            protocol: 'amqp',
            port: 5672,
            locale: 'en_US',
            frameMax: 0,
            heartbeat: 0,
            vhost: '/',

        }, options);

        this._conn = null;
        this._inited = false;

        this._channelMap = {

        };

        this._operatorMap = {

        };
    }

    async init() {
        if (this._inited) {
            return;
        }

        const conn = await connect(this._options);
        this._conn = conn;
        this._inited = true;
    }

    async _getChannel(queue) {
        let channel;

        if (this._channelMap[queue]) {
            channel = this._channelMap[queue];
        } else {
            channel = await this._conn.createChannel();
            channel.assertQueue(queue);
            this._channelMap[queue] = channel;
        }
        return channel;
    }

    channel(queue) {
        if (queue.trim() === '') {
            throw new Error('queue name should not be empty!');
        }
        if (this._operatorMap[queue]) {
            return this._operatorMap[queue];
        }

        const self = this;

        const operator = {
            send: async function(msg) {
                const channel = await self._getChannel(queue);
                if (typeof msg === 'object') {
                    msg = JSON.stringify(msg);
                }

                channel.sendToQueue(queue, Buffer.from(msg));
            },
            subscribe: async function(func) {
                const channel = await self._getChannel(queue);
                channel.consume(queue, async function(msg) {
                    let status = true;
            
                    try {
                        let data = msg.content.toString();
                        
                        try {
                            if (data && data[0] === '{') {
                                data = JSON.parse(data);
                            }
                        } catch(err) {
                            console.error(err);
                        }

                        await func(data);
                    } catch(err) {
                        console.error(err);
                        status = false;
                    }
            
                    if (status) channel.ack(msg);
                });
            }
        };

        this._operatorMap[queue] = operator;
        return operator;
    }

}
```

