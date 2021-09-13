---
title: iterator(迭代器)
date: 2018-08-30 12:38:46
tags: [ nodejs, python ]
---

Node 版本:

```javascript
function * hello(max) {
    let count = 0;

    let a = 1, b = 1, tmp = 1;

    while(count < max) {
        yield a;
        tmp = a;
        a = b;
        b = tmp + b;
        count ++;
    }
}

for(const i of hello(500)) {
    console.log(i);
}
```



Python 版本:

```python
def fab(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1

for i in fab(5):
    print(i)
```





两者都是通过`yield`关键字来返回迭代值，并中断函数(保留当前的状态)。迭代器下次被调用时，才重新开始从 yield 后面开始运行。

区别一点是，Python 只要包含 yield 这个关键字，整个`fab(x)`就是生成器。而 Node 需要 在 function 后面加`*`才行。否则会报错。




