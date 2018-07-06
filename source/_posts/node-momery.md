---
title: 取消 Node 对内存上限的限制条件
date: 2018-06-06 16:36:15
tags: [ Node.js ]
---

```shell
node --max-old-space-size=1700 test.js // 单位为MB
// or
node --max-new-space-size=1024 test.js // 单位为KB
```

