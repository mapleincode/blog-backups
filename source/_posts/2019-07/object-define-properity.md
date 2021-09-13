---
title: Vue.js 数据绑定原理
date: 2018-04-12 17:17:03
tags: [ javascirpt, vue ]
---

作为一个后端开发，对于`set` 和`get`一般都不会陌生。在面向对象开发中，一个对象一般都有 get 和 set 的属性，赋值既是`set`，得值就是`get`。刚接触 JavaScript 的时候，似乎还没有这个特性（也可能自己太菜），随着 Vue.js 的流行慢慢火热起来。其实原理很简单，就是看怎么搞。

## 对象描述类型

通俗的来说就是一个对象`假设 var obj = { a: 1 }`，有一种东东叫做属性描述对象，用于解释 `obj.a`的一些属性。

有且只有两种类型: 

1. 数据型描述符
2. 控制型描述符

所有对象的 key 的描述对象类型必须是两者之一。

#### 数据型描述符(data descriptors)

也是默认的方式，拥有以下四个元属性:

1. value

   `value`是该属性的属性值，默认为`undefined`。

2. writable

   `writable`是一个布尔值，表示属性值（value）是否可改变（即是否可写），默认为`true`。

3. enumerable

   `enumerable`是一个布尔值，表示该属性是否可遍历，默认为`true`。如果设为`false`，会使得某些操作（比如`for...in`循环、`Object.keys()`）跳过该属性。

4. configurable

   `configurable`是一个布尔值，表示可配置性，默认为`true`。如果设为`false`，将阻止某些操作改写该属性，比如无法删除该属性，也不得改变该属性的属性描述对象（`value`属性除外）。也就是说，`configurable`属性控制了属性描述对象的可写性。

#### 控制性描述符(accessor descriptors)

也有用`enumberable`和`configurable`，定义内容也基本一致。区别是另外两个是 `get` 和 `set`相当于面向对象的`setter`和`getter`。

1. `get`

   ​    `get`是一个函数，表示该属性的取值函数（getter），默认为`undefined`。

2. `set`

   `set`是一个函数，表示该属性的存值函数（setter），默认为`undefined`。

## Object.getOwnPropertyDescriptor()

获得属性描述对象。

```js
var obj = { p: 'a' };

Object.getOwnPropertyDescriptor(obj, 'p')
// Object { value: "a",
//   writable: true,
//   enumerable: true,
//   configurable: true
// }
```

## Object.getOwnPropertyNames()

```javascript
Object.keys([]) // []
Object.getOwnPropertyNames([]) // [ 'length' ]

Object.keys(Object.prototype) // []
Object.getOwnPropertyNames(Object.prototype)
// ['hasOwnProperty',
//  'valueOf',
//  'constructor',
//  'toLocaleString',
//  'isPrototypeOf',
//  'propertyIsEnumerable',
//  'toString']
```

和 `Object.keys()`的区别:

1. 无论 `enumerable` 是否， `Object.getOwnPropertyNames()`全部返回，而`Object.keys()`只会返回为`true` 的属性。



## Object.defineProperty()（Object.defineProperties()）

定义属性描述的方法。

```javascript
Object.defineProperty(object, propertyName, attributesObject)
```

```javascript
var obj = {};
Object.defineProperty(obj, 'aaa', {
    get: function() {
        return '1';
    }
});
```

`Object.defineProperties()`用于一次修改多个属性

```javascript
var obj = Object.defineProperties({}, {
  p1: { value: 123, enumerable: true },
  p2: { value: 'abc', enumerable: true },
  p3: { get: function () { return this.p1 + this.p2 },
    enumerable:true,
    configurable:true
  }
});
```

## Object.prototype.propertyIsEnumerable()

实例对象的`propertyIsEnumerable`方法返回一个布尔值，用来判断某个属性是否可遍历。

```javascript
var obj = {};
obj.p = 123;

obj.propertyIsEnumerable('p') // true
obj.propertyIsEnumerable('toString') // false
```



## 然后就是 Vue.js 的实现了

表示没看过源码，所以这是一个小 demo。但是实际实现方式应该就是这样。

```javascript
class Vue {
    constructor(config) {
        const data = config.data;
        const keys = Object.keys(data);

        this.$data = {};

        for(const key of keys) {
            this.$set(key, data[key]);
        }

    }


    $set(key, value) {
        const descriptor = Object.getOwnPropertyDescriptor(this, key);

        if(descriptor === undefined || descriptor.writable !== undefined) {
            Object.defineProperty(this, key, {
                get: function() {
                    return this.$data[key];
                },
                set: function(value) {
                    console.log(`有 key: ${key} 的值由 ${this.$data[key]} 变成了 ${value}`);
                    this.$data[key] = value;
                }
            });
        }

        this[key] = value;
    }
}

const vue = new Vue({
    data: {
        a: 1,
        b: 2,
        c: 3
    }
})

// 有 key: a 的值由 undefined 变成了 1
// 有 key: b 的值由 undefined 变成了 2
// 有 key: c 的值由 undefined 变成了 3

vue.a = 11; // 有 key: a 的值由 1 变成了 11
vue.b = 22; // 有 key: b 的值由 2 变成了 22
vue.c = 33; // 有 key: c 的值由 3 变成了 33

vue.d = 4; // no output
vue.$set('d', 4); // 有 key: d 的值由 undefined 变成了 4
vue.d = 44; // 有 key: d 的值由 4 变成了 44
```




