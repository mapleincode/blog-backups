---
title: redis 学习笔记
date: 2018-07-03 18:02:16
tags: [ redis ]
---
# Redis 学习笔记

1. redis 支持五种数据类型: String, Hash, List, Set, ZSet。

## KEY

```sh
set key
get key
del key
dump key // 序列化给定 key，并返回而序列化的值。
exists key
expire key // 秒
expireat key // timestamp
pexpireat key // 毫秒 (unix timestamp)
keys pattern // 查找所有符合给定模式(pattern)的 key
move key db // 移动 key 到另一个 db
persist key // 移除 key 的过期时间
pttl key // 返回 key 的剩余时间 毫秒
ttl key // 返回 key 的剩余时间 秒
randomkey // 随机返回一个 key
rename key newkey // 重命名
renamenx key newkey // 仅 newkey 不存在时，重命名
type key // 返回 key 所存储的值的类型
```

## String

```sh
set key value
get key
getrange key start end
getset key value // 设定 key 的新值，并返回老的值
getbit key offset // 对 key 所存储的字符串值，获取指定偏移量上的位(bit)
mget key [key2...]
setbit key offset value // 对 key 所存储的字符串值，设置或清除指定偏移量上的位(bit)
setex key seconds value // 设置 key 的value 并设置过期时间 seconds(s)
setnx key value // 只有在 key 不存在的时候设置 key 的值
setrange key offset value // 用 value 参数覆盖写的给 key 所存储的字符串值，从偏移量 offset 开始
strlen key //字符串的长度
mset key value [key value]
msetnx key value [key value] // 同事设置一个或多个 key-value 时，当且在给定的 key 不存在。
incr key // 默认 0.原子操作
incrby key increment // 增量值 (increment)
incrbyfloat key increment // 浮点增量值
decr key
decrby key decrement // 减量值
append key value // 如果 key 存在且为字符串，追加 value 到该 key。
```

## Hash

```
hdel key field1 [field2]
hexists key field
hget key field
hgetall key
hincrby key field increment
hincrbyfloat key field floatincrement
hkeys key // 获取所有哈希表中的字段
hlen key // 获得哈希表中字段的数量
hmget key field1 [field2]
hmset key field1 value1 [field2 value2]
hset key field value
hsetnx key field value
hvals key // 获得哈希表中所有值
hscan key cursor [MATCH pattern] [COUNT count]
```



## List

```
blpop key [key2] timeout // pop left 列表。如果列表没有元素就堵塞，直到有元素或者超时。
brpop key [key2] timeout // pop right 列表。如果列表没有元素就堵塞，直到有元素或者超时。
brpoplpush source destination timeout // 将一个元素从 A 列表弹出并插入到 B 列表。并返回。如果没有元素就等待超时。

```


