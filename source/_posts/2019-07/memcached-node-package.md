---
title: Nodejs memcached 包的基本用法 
date: 2018-07-05 18:38:41
tags: [ nodejs, memcached]
---

```javascript
const Memcached = require('memcached');
const memcached = new Memcached('127.0.0.1:11211');

/**
 * 设置 key 的过期时间(s)
 */
memcached.touch('foo', 10, function(err) {
    console.log(err);
});

/**
 * 获得 key 为 foo 的值
 */
memcached.get('foo', function(err, value) {
    console.log(err, value);
});

/**
 * 获得 key 为 foo 的值和 cas (Check And Set)
 * cas 用于 memcached.CAS 更新
 */
memcached.gets('foo', function(err, data) {
    console.log(data.foo);
    console.log(data.cas); 
});

/**
 * 同时获取 key 为 foo, bar 的值
 */
memcached.getMulti([ 'foo', 'bar' ], function(err, data) {
    console.log(data.foo);
    console.log(data.bar);
});

/**
 * 设置 key 为 foo 的值为 bar。并设置过期时间为 10 s。
 */
memcached.set('foo', 'bar', 10, function(err) {

});

/**
 * 更换 key 为 foo 的值为 bar。
 * 如果 key 不存在，则报错。
 */
memcached.replace('foo', 'bar', function(err) {

});

/**
 * 添加 key 为 foo 的值为 bar。并设置过期时间为 10s。
 * 如果 key 已存在，则报错
 */
memcached.add('foo', 'bar', 10, function(err) {

});

/**
 * 通过 gets() 方法获得 cas。
 * 然后通过 cas() 和 cas 值对key foo 进行更新值和过期时间。
 * 如果 cas 不存在报错。
 * 如果 cas 与值本身的 cas 不同也报错。
 */
memcached.gets('foo', function(err, data) {
    const cas = data.cas;
    memcached.cas('foo', 'bar', data.cas, 10, function(err) {

    });
});

/**
 * 在 key 为 foo 的值前面拼接 bar。
 */
memcached.append('foo', 'bar', function(err) {

});

/**
 * 在 key 为 foo 的值后面拼接 bar
 */
memcached.preprend('foo', 'bar', function(err) {

});

/**
 * 对于 key 为 foo 的值加 10。
 */
memcached.incr('foo', 10, function(err) {

});

/**
 * 对于 key 为 foo 的值减 10。
 */
memcached.decr('foo', 10, function(err) {

});


/**
 * 删除 key 为 foo 的值
 */
memcached.del('foo', function(err) {
    
});

```
