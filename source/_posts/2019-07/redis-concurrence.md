---
title: node.js 请求 redis 的性能优化和 redis 的管道和事务机制
date: 2019-03-13 17:16:44
tags: [ Node.js, Redis ]
---

最近做了一个项目，涉及了大量的 redis IO 读写。

原先的方式采用了单命令，一个操作往往涉及多个请求，写入和读取数据的效率十分低。因此，期望通过打包命令的方式，来减少网络请求 IO 对效率的影响

在网上找到某大佬关于 redis 单条命令请求效率低的解释:

> 1. socket IO 导致的上下文切换开销
>    - 熟悉 OS／Linux 的童鞋都知道，一次 redis 请求在客户端和服务端分别至少会存在一次 read() 和一次 write() ，作为系统调用，read／write 的成本高于普通的函数调用，因此，在单个命令重复调用场景下，大量的 read/write 系统调用会产生明显的系统开销。
> 2. 指令执行开销
>    - Redis 采用 C 实现，使用了轻量级的 hash 表、skipList 跳表等数据结构实现了高效的缓存。因此，单条执行大多数指令的成本非常低。因此，相对而言，IO 的开销显得更加无法忽略。
> 3. (高并发下) 资源竞争和系统调度调度开销
>    - 一般来说，这一开销在客户端的影响更为明显。在高压力下，如果采用循环( loop )方式调用多次指令来完成某个服务请求，那么在高并发下，多个请求会在多个线程中同时竞争 redis 连接资源多次，导致连接池压力增加，线程上下文切换更加频发，最终会导致请求 RTT(round-trip time) 急剧恶化。如果每个请求只抢占一次 redis 连接并通过批量执行的方式一次处理多个请求，则单次请求的 RTT 会有显著提升。
>    - 在服务端，因为我们通常将 redis 绑定到 CPU (不管是通过物理机还是通过docker)，因此一般而言不存在系统调度／资源竞争的开销。但是由于 redis 对 QPS 敏感，如果因为客户端使用不合理而造成 qps 放大效应，则 redis 可能更早触及性能瓶颈而导致系统响应严重下降。
>
> FROM https://www.jianshu.com/p/75137d23ae4a

---

Redis 的批量请求一般有以下几种方式。

- 批量get/set(multi get/set)
- 管道(pipelining)
- 事务(transaction)
- 基于事务的管道(transaction in pipelining)

> 虽然个人很不认同第三种，刚开始很不理解为什么文章实验结果出来 3、4 类型的时间基本相似。后面看了 ioredis 的说明才明白。可能他调用的 module 本身默认在事务的情况下开启了管道。

## Demo

使用的 redis 封装模块包是 ioredis。 [https://www.npmjs.com/package/ioredis](https://www.npmjs.com/package/ioredis)。

ioredis 功能 README 都有说明。

这里简单做一下笔记，以免之后忘记。

## 批量请求命令

批量请求命令主要有:

1. mset
2. mget
3. hmset
4. hmget

### mset

```javascript
let result = await redis.mset({ key1: 'value1', key2: 'value2' });
let result = await redis.mset(key1, 'value1', key2, 'value2');
result; // [ 'ok', 'ok' ]
```

### mget

```javascript
let result = await redis.mget([ 'key1', 'key2' ]);
let result = await redis.mget('key1', 'key2');
result; // [ 'value1', 'value2' ]
```

### hmset

```javascript
await redis.hmset('key', { k1: 'v1', k2: 'v2' })
await reids.hmset('key', new Map([['k1', 'v1'], ['k2', 'v2']]))
await redis.hmset('key', 'k1', 'v1', 'k2', 'v2')
```

### hmget

```javascript
await redis.hmget('key', [ 'k1', 'k2' ]);
await redis.hmget('key', 'k1', 'k2');
```



## 管道(Pipelining)

 redis 的管道通俗的来说就是将所有请求打包一个命令数组，在服务器上触发命令，并将结果包装成一个数组返回到客户端。

```javascript
const pipeline = redis.pipeline();
pipeline.set('foo', 'bar');
pipeline.del('cc');
let result = await pipeline.exec();
redis;
// [ [ null, 'ok'], [ null , 'ok'] ]
```

返回的 result 被包装成由 [ err, result ] 组成的数组。

管道默认是不支持事务的，也就是无论是否有命令报错，所有命令都会依次请求一次。

所以需要自己根据返回的 result 处理错误的命令。

管道命令也支持菊花写法：

```javascript
await redis.pipeline().set('foo', 'bar').del('cc').exec();
```

还能支持数组式的写法

```javascript
await redis.pipeline([
    [ 'set', 'foo', 'bar' ],
    [ 'del', 'cc' ]
]).exec();
```

## 事务(Transaction)

关于数据库的事务，一直以来给我两个印象:

1. 支持错误回退。
2. 会降低数据库的性能。

好在 redis 是单进程系统，所以不会因为事务来导致锁表锁行等一些严重降低性能的行为。

---

这里主要说明的是，redis 对待两种不同的错误类型有两种不同的策略。

**第一类是代码错误或者系统错误。**

指的是某个代码命令有误或者系统错误。

后者例如系统故障、内存不足等我们最担心的错误类型。

这些命令会在请求入栈时报错，然后在 exec 返回错误。

但是在执行 exec 时，会被 redis 拒绝。

```bash
redis> multi
OK
redis> set key world
QUEUED
redis> hset
ERR wrong number of arguments for 'hset' command
redis> exec
EXECABORT Transaction discarded because of previous errors.
```

总结来说有三种原因:

1. 系统、内存报错等不可抗拒的错误
2. 命令的名称错误
3. 命令的参数错误

这种原因下的报错的结果是**任何数据都不会被修改**。



**第二类是代码逻辑错误。**

一般产生原因是对 key 进行错误的(逻辑)操作，例如：

```bash
> multi
OK
> set key world
query
> incr key
query
> exec
1 OK
2 ERR value is not an integer or out of range
```

`incr key` 本身并没有预发错误，但是 key 被预先设置为字符串 key。因此在执行`incr` 命令时发生报错。

**对于这一类逻辑错误，redis 的执行方式是不做任何处理**

除了错误的那条命令发生错误，返回错误之外，别的命令都正常执行。

关于原因，有人解释说，对于逻辑错误，本身的原因应该是程序在开发过程中的 BUG 导致的，而不是因为随机性因素。如果因为逻辑错误产生的问题，不应该依靠事务来解决。

---

ioredis 默认对事物开启管道：

```javascript
redis.multi().set('foo', 'new value').exec(function (err, results) {
});
```

因为 ioredis 并未提供 discard 命令。因此可以理解成 ioredis 的开发者是强烈推荐使用默认(管道)的方式来执行事务。

也可以有不开启管道的办法:

```javascript
redis.multi({ pipeline: false });
redis.set('foo', 'bar');
redis.get('foo');
redis.exec(function (err, result) {
  // result === [[null, 'OK'], [null, 'bar']]
});
```



