---
title: memcached 实现高并发条件下全局锁
date: 2021-09-13 15:10:20
tags: [ memcached ]
---

在支持 redis 的项目中，我很喜欢用

```
redis.incr();
```

因为 redis 操作具有原子性，可以通过这个函数实现原子级的同步锁。

而 memcache 却不能实现这样的效果，我考虑设想了了一个方案。



```javascript
async funciton lock(key) {
  const value = uuid.v4();
  const expire = 120 * 1000;
  
  const _result = await mem.get(key);
  if (_result) return false;
  
	await mem.set(key, value);
  await sleep(1000);
  const result = await men.get(key);
  if (result === key) {
    return true;
  }
  return false;
}
```



优点：

- 支持同步
- 支持高并发



缺点：

- 操作会滞后，需要 await 1s 或者 500ms 左右。保证后面的操作不会因为读取到空值再覆盖写入
- 理论上，如果请求足够多，会造成 lock 不停的被复写（实际上小项目很难达到这样的并发）。