---
title: redis 学习笔记
date: 2018-07-03 18:02:16
tags: [ Redis ]
---
## Redis 数据类型

1. redis 支持五种数据类型
   - String: 字符串
   -  Hash: Hash 散列
   - List: 列表
   - Set: 集合
   - ZSet: 有序集合(Sorted Set)

## KEY

```bash
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

```bash
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

```bash
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

```bash
blpop key [key2] timeout // pop left 列表。如果列表没有元素就堵塞，直到有元素或者超时。
brpop key [key2] timeout // pop right 列表。如果列表没有元素就堵塞，直到有元素或者超时。
brpoplpush source destination timeout // 将一个元素从 A 列表弹出并插入到 B 列表。并返回。如果没有元素就等待超时。
lindex key index // 通过索引获得列表中的元素
linsert key BEFORE|AFTER pivot value // 在列表 pivot 元素<前|后>插入数据
llen key // 列表长度
lpop key // list pop
lpush key value k v k1 v1 // list push
lpushx key value // list push 列表不存在时操作无效
lrange key start stop //获取指定范围的元素 。index 从 0 开始。
lrem key count value // 移除列表元素 count > 0 表头开始 count < 0 表尾开始 count=0 移除所有
lset key index value
ltrim key start stop // 剪辑 key 的元素
rpop key // rpop key
rpoplpush source destination // 移除列表最后一个元素并把其添加到另外一个列表并返回
rpush key value [value2] // push 到队列尾部
rpushx key value // push 到队列尾部，队列不存在则操作无效
```

## Set

```bash
sadd key member1 [member2]
scard key // 获得成员数
sdiff key [key2] // 返回两个集合的差集
sdiffstore destination key1 [key2] // 返回给定集合的差集并存储到 destination 中
sinter key1 [key2] // 返回两个集合的交集
sinterstore destination key1 [key2] // 返回两个集合的交集并存储到 destination 中
sismember key member // 判断 member is member of Set
smembers key // 返回集合中所有的成员
smove source destination member // 把 member 从一个集合移动到另个集合
spop key  // 随机移除一个成员并返回
srandmember key [count] // 返回集合中的随机一个 or 多个
srem key member1 [member2] // remove 成员
sunion key [ key2] // 返回两个集合的并集
sunionstore destination key1 [key2] // 返回集合的并集并存储到 destination
sscan key cursor [MATCH pattern] [COUNT count] // 匹配返回 key: key, cursor: index, match 正则, count 数量
```



## 有序集合

```
zadd key score1 member1 [score2 member2] 
zcard key // 获取成员数
zcount key min max // 获得某个区间的成员数
zincrby key increment member
zinterstore destination numkeys key[key ..] // 计算若干个 key 的分数交集并保存到 destination
zlexcount key min max // 命令在计算有序集合中指定字典区间内成员数量 在 score 相同的情况下可以按照 member 进行排序按照 member 计算数量
zrange key start stop [WITHSCORES] // 返回索引区间的 member
zrangebylex key min max [LIMIT offset count] // 通过字典序返回 member
zrangebyscore key min max [WITHSCORES] [LIMIT] // 通过 score 返回 member
zrank key member// 返回有序集合指定成员的索引
zrem key member [member...] // 移除成员
zremrangebylex key min max // 根据字典序移除成员
zremrangebyrank key start stop // 根据排名区间移除成员
zrevrank key member // 返回某个成员的排名
zscore key member// 返回某个成员的 score
zunionstore destination numkeys key [key...] // 计算 num 个 key 的 member 的 并集并存储到 destination

```












