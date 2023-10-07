---
date: 2019-12-16 17:26:40
title: Koa-Compose 源码试写
---



```javascript
async function _compose(ctx, next, items) {
    if(items.length === 0) {
        return next();
    }

    const func = items.shift();
    return func(ctx, async function() {
        return _compose(ctx, next, items);
    });
}

function compose(items) {
    return async function(ctx, next) {
        return _compose(ctx, next, items);
    }
}

const items = [
    function(ctx, next) {
        console.log('a.start');
        next();
        console.log('a.end');
    },

    function(ctx, next) {
        console.log('b.start');
        next();
        console.log('b.end');
    },

    function(ctx, next) {
        console.log('c.start');
        next();
        console.log('c.end');
    },
]

compose(items)({}, function() { console.log('end') });
```

注意几点是，如果有函数 `func `就必须以 `await func()` 的方式来运行，这样保证 func 无论是普通函数或者 `promise` 函数，都可以正常运行。同样的还有 `await next()` 函数

---

发现比较有趣的一点，如果是 async 函数，最后的结果是 `return await main()`，那就会和 `return main()` 结果一致。当然实际运行的过程却是不一样的。

```javascript
async main() => {
	return await done();
}
```

相当于在`main()` 的生命周期内，先执行 `done()`，然后把返回值返回。因为是在 async 函数中，返回时会重新用 `Promise `封装一层。最后返回一个 Promise 对象。



而 

```javascript
async main() => {
	return done();
}
```

就完全不一样，在`main()` 的生命周期中, `done()` 只是返回一个 promise 对象，并同步开始执行(前者是已经执行完毕)。即使到最后 async 封装一层 Promise 之后，函数其实也并未执行完成。而是等待下一个 await 方法来返回对象。



虽然后者的功能一致并且可以省略一个语法词，但是推荐还是用前者的办法来编写。因为前者在修改变量也丝毫不影响，但是后者因为是 promise 对象不能直接修改。

```javascript
async main() => {
	const a = await done();
  return a + 1;
}

async main() => {
	const a = done();
  return a + 1; // error
}
```

