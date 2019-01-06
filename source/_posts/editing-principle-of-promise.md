---
title: Promise 原理和简单 Demo
date: 2018-11-01 11:25:38
tags: [ JavaScript ]
---

Promise 从被实现已经很久了，但是我的确很少关注这块点。以至于到最后出现 async/await 就相当于直接跳过了 Promise 的使用。对我来说， async/await 的概念更容易让我接受。以至于某一次面试，面试官问我 Promise 的原理我一丁点都答不上来。



首先，Promise 是一个类型，支持传入一个 function 匿名函数。

```javascript
class Promise {
    constructor(func) {
        this.func = func;
    }
}
```

