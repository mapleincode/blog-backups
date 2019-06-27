---
                           title: JavaScript Proxy 对象
date: 2018-11-01 11:22:35
tags: [ JavaScript, Node.js ]
---

## Usage

```javascript
let target = {};
let handler = {};
let p = new Proxy(target, handler);
```

**target: 目标对象。可以是任何类型的对象，甚至是另一个代理。**

**handler: 一个对象，其属性是当实行一个操作是定义代理的行为的函数。**



## Example

```javascript
let handler = {
    get: function(target, name) {
        return name in target ? target[name] : 'hellonovalue'
    }
};
let p = new Proxy({}, handler);

p.a = 1;
p.b = undefined;

console.log(p.a, p.b) // 1, undefined

console.log('c' in p, p.c) // false, 37
```

## 作用

`target` 是目标对象，用一个代理，通过 handler 定义规则，来对 target 对象的操作进行封装。

相当于 target 只负责保存数据，proxy 才是真的有规则的对象。

也可以用于对 target 的规则进行修改。（例如 target 是一个已知 Method 的对象，Proxy 可以达到对其增加功能的效果）。

如果 Handler 并未定义，则所有操作无条件转发到目标对象。

```javascript
let target = {};
let p = new Proxy(target, {});

p.a = 37;
target.a === 37; // true
```

## GET

```javascript
let handler = {
    get: function(target, name) {
    	if(name !== 'world') return target[name];
        return 'hello world';
    }
};
```

## SET

```javascript
let handler = {
    set: function(target, prop, value) {
        if(prop === 'age' && !Number.isInteger(value)) {
            throw new TypeError('The age is not an integer');
        } 
        target[prop] = value;
    }
}
```



## 实现简单的 demo

```javascript
let handler = {
    get: function(target, name) {
        const send = target.send;

        return send.bind(target, name);
    }
};

class Done {
    constructor() {
        this.startId = 0;
    }

    send(value, nextValue) {
        console.log(`id is ${this.startId ++} and value is ${value}, ${nextValue}!`);
    }
}

function init() {
    const done = new Done();
    return new Proxy(done, handler);
}

const end = init();

end.hello('world'); // 等同 end.send('hello', 'world');
end.你好('世界');
```

