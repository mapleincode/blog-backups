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

```javasc
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

## 扩展构造函数

**`Object.getOwnPropertypDescriptor`**

获得对象属性描述。

```javascript
function Person(name) {
    this.name = name;
}
Object.getOwnPropertypDescriptor(Person.prototype, 'constructor'); 
// { value: [Function: Person],
//  writable: true,
//  enumerable: false,
//  configurable: true }
```



```javascript
function extend(sup, base) {
    let descriptor = Object.getOwnPropertyDescriptor(
    	base.prototype, 'construsctor'
    );
    base.prototype = Object.create(sub.prototype);
    let handler = {
        construct: function(target, args) {
            let obj = Object.create(base.prototype);
            this.apply(target, obj, args);
        },
        apply: function(target, that, args) {
            sup.apply(that, args);
            base.apply(that, args);
        }
    };
    
    let proxy = new Proxy(base, handler);
    descriptor.value = proxy;
    Object.defineProperty(base.prototype, 'constructor', descriptor);
    return proxy;
}

let Person = function(name) {
    this.name = name;
};

let Boy = extend(Person, function(name, age) {
    this.age = age;
});

Boy.prototype.sex = 'M';

let Peter = new Boy('Peter', 13);

console.log(Peter.sex);  // "M"
console.log(Peter.name); // "Peter"
console.log(Peter.age);  // 13
```

