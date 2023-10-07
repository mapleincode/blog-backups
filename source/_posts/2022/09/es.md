---
date: 2022-09-26 11:11:59
title: ES 版本新特性
tags: [JavaScript]
---



## 环境

```bash
$ node -v
v18.9.1
```



## ES6 (2015)

```javascript
// class
class Person {
  constructor(name) {
    this.name = name;
  }
  
  payMoneny() {
    
  }
}

class Doctor extend Person {
  constructor(name) {
    super(name);
  }
  
  receiveMoney() {
    
  }
}

// export module
export var xx = xx;
export function main() {};

// module default
export default xx;

// import
import xx from 'xx';
import * as yy from 'xx';
import xx, { zz } from 'xx';

// lambda
() => {};
a => !a;
a => ({a: a});

// 函数默认值
function foo(bar = 'xx') {}

// 模板字符串
let a = `
a
`;
let b = `${a}`;

// 解构赋值
const [a, b] = [1, 2];
const { a, b } = { a: 1, b: 2};
const [a, b = 2] = [1]

// 延展操作符(Spread operator)
a = [1, 2, 3];
foo(...a);
b = [...a, ...'hello'];

// es2018 ?
c = { a: 1, b: 2 };
d = { ...c, b: 3, d: 4 };

// 对象简写
name = 'test';
ago = 20;
man = {
  name,
  ago
}

// Promise
xx
	.then(() => {})
  .catch(e => {})

// let & const 
let a=1; 
{
  let a=2
  {
    let a=3;
    console.log(a)
  }
  console.log(a)
}
console.log(a)


```





## ES7 (2016)

```javascript
[1, 2, 3].includes(1) // true

2 ** 10 // 1024
```



## ES8 (2017)

```javascript
// async & await
async function a() {
  
}

async function b() {
  return await a();
}

b()
	.catch(e => console.error(e));

// Object.values & Object.entries
a = {b:1, c:2};
Object.values(a); // [1, 2]
Object.entries(a); // [ [ 'b', 1 ], [ 'c', 2 ] ]
for(const [ key, value ] of Object.entries(a)) {
  console.log(key, value);
}

// padStart & padEnd
b = '1';
b.padStart(3, '0'); // 001
b.padEnd(3, '0'); // 100

// getOwnPropertyDescriptors
const obj2 = {
	name: 'Jine',
	get age() { return '18' }
};
Object.getOwnPropertyDescriptors(obj2)
// {
//   name: {
//     value: 'Jine',
//     writable: true,
//     enumerable: true,
//     configurable: true
//   },
//   age: {
//     get: [Function: get age],
//     set: undefined,
//     enumerable: true,
//     configurable: true
//   }
// }

// SharedArrayBuffer
new SharedArrayBuffer(length)

// Atomics
Atomics.add() // 将指定位置上的数组元素与给定的值相加，并返回相加前该元素的值。
Atomics.and() // 将指定位置上的数组元素与给定的值相与，并返回与操作前该元素的值。
Atomics.compareExchange() // 如果数组中指定的元素与给定的值相等，则将其更新为新的值，并返回该元素原先的值。
Atomics.exchange() // 将数组中指定的元素更新为给定的值，并返回该元素更新前的值。
Atomics.load() // 返回数组中指定元素的值。
Atomics.or() // 将指定位置上的数组元素与给定的值相或，并返回或操作前该元素的值。
Atomics.store() // 中指定的元素设置为给定的值，并返回该值。
Atomics.sub() // 将指定位置上的数组元素与给定的值相减，并返回相减前该元素的值。
Atomics.xor() // 将指定位置上的数组元素与给定的值相异或，并返回异或操作前该元素的值。
// wait() 和 wake() 方法采用的是 Linux 上的 futexes 模型（fast user-space mutex，快速用户空间互斥量），可以让进程一直等待直到某个特定的条件为真，主要用于实现阻塞。
Atomics.wait() // 检测数组中某个指定位置上的值是否仍然是给定值，是则保持挂起直到被唤醒或超时。返回值为 "ok"、"not-equal" 或 "time-out"。调用时，如果当前线程不允许阻塞，则会抛出异常（大多数浏览器都不允许在主线程中调用 wait()）。
Atomics.wake() // 唤醒等待队列中正在数组指定位置的元素上等待的线程。返回值为成功唤醒的线程数量。
Atomics.isLockFree(size) // 可以用来检测当前系统是否支持硬件级的原子操作。对于指定大小的数组，如果当前系统支持硬件级的原子操作，则返回 true；否则就意味着对于该数组，Atomics 对象中的各原子操作都只能用锁来实现。此函数面向的是技术专家.
```





## ES9 (2018)

```javascript
// 异步迭代
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
}
// nodejs 环境一直都是支持的，不需要 for await

// Promise.finally()
xx()
	.finally(() => {});

function(a,b, ..c) {}

// 正则表达式命名
const
  reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
  match  = reDate.exec('2018-04-30'),
  year   = match.groups.year,  // 2018
  month  = match.groups.month, // 04
  day    = match.groups.day;   // 30

const
  reDate = /(?<year>[0-9]{4})-(?<month>[0-9]{2})-(?<day>[0-9]{2})/,
  d      = '2018-04-30',
  usDate = d.replace(reDate, '$<month>-$<day>-$<year>');

// 正则表达式反向断言
const
  reLookahead = /\D(?=\d+)/,
  match       = reLookahead.exec('$123.89');

console.log( match[0] ); // $

// 正则 dotAll
/hello.world/.test('hello\nworld');  // false
/hello.world/s.test('hello\nworld'); // true

// 正则 unicode 转义
const reGreekSymbol = /\p{Script=Greek}/u;
reGreekSymbol.test('π');



// 非转义序列的模板字符串
function latex(str) {
 return { "cooked": str[0], "raw": str.raw[0] }
}
latex`\unicode` // // { cooked: undefined, raw: "\\unicode" }
```





## ES10 (2019)

```javascript
// 行分隔符（U + 2028）和段分隔符（U + 2029）符号现在允许在字符串文字中，与 JSON 匹配
// 输入 Unicode 格式但是超出范围的字符，在使用 JSON.stringify 时，使它为其输出转义序列，使其成为有效Unicode（并以UTF-8表示）

// Array.prototype.flat
let arr1 = [1, 2, [3, 4]];
arr1.flat(); 
// [1, 2, 3, 4]

let arr3 = [1, 2, [3, 4, [5, 6]]];
arr3.flat(2);
// [1, 2, 3, 4, 5, 6]

// Array.prototype.flatMap
arr1.flatMap(x => [x * 2]);
// [2, 4, 6, 8]

// trimStart() trimEnd()

// Object.fromEntryies();
const obj = Object.fromEntries([ ['foo', 'bar'], ['baz', 42] ]);
console.log(obj); // { foo: "bar", baz: 42 }


// Symbol.prototype.description

// String.prototype.matchAll
const regexp = RegExp('foo*','g');
const str = 'table football, foosball';

while ((matches = regexp.exec(str)) !== null) {
  console.log(`Found ${matches[0]}. Next starts at ${regexp.lastIndex}.`);
  // expected output: "Found foo. Next starts at 9."
  // expected output: "Found foo. Next starts at 19."
}
// matchAll 和 String.prototype.match 区别是，前者返回的迭代器，后者是数组。

// Function.prototype.toString()
function /* comment */ foo /* another comment */() {}
// ES2019 会把注释一同打印，箭头函数不影响
console.log(foo.toString()); // function /* comment */ foo /* another comment */ (){}

// catch 
try {} catch {}

// BigInt
let big = 1000n;
typeof big // bigint
```



## ES11 (2020)

```javascript
// 动态 imports
const a = await import(`${basePath}/xx.js`);

// import.meta

// export
export * as {myModule} from "./test-module.js";

// globalThis 引入，代替 global 和 self

// Promise.allSettled()
const promises = [promise1("/do1"), promise2("/do2")];
const allResults = await Promise.allSettled(promises);
const errors = results
  .filter(p => p.status === 'rejected')
  .map(p => p.reason);

// ??
undefined || 18   // 18
0 || 18           // 18 (null, undefined, false)
0 ?? 18           // 0
undefined ?? 18   // 18
null ?? 18        // 18
false ?? 18       // false

// ?.
const age = school?.class?.student?.age;
const age = student.getAge?.();
```



## ES12 (2021)

> nodejs 18 部分支持

```javascript
// String.prototype.replaceAll
'1 2 1 2 1 2'.replaceAll('2', '0'); // '1 0 1 0 1 0'

// Promise.any
Promise.any([
    Promise.reject('Error 1'),
    Promise.reject('Error 2'),
    Promise.resolve('success'),
]).then((result) => {
    console.log('result:', result);
}); // success

// WeakRefs
// 对内存大小不一样的设备实现不一样的效果（小内存被回收，大内存依然存在)
function Foo() {}
// strong reference to a Foo instance
const x = new Foo();
// weak reference to the Foo's instance
const xWeak = new WeakRef(x);
// strong reference to the Foo's instance
const xFromWeak = xWeak.deref();

// Finalizers
let x = new Array(1000).fill(true);
// constructing the finalizer method
const cleanup = new FinalizationRegistry(key => {
  // cleanup code should go here
});
// hooking the x variable to the finalizer
cleanup.register(x, 'fsdfs');

// ||= &&= ??=
x &&= y // x && x = y
x ||= y // x || x = y
x ??= y // x ?? x = y

// Numeric Separators, python 好像很早就支持
const oneMillion = 1_000_000;
```



## ES13 (2022)

```javascript
// class 私有
class Person {
	#name = 'test';
  
  get #school() {
    return '123'
  }
  
  static #count = 1;
	
	static #addCount() {
    this.#count++;
  }

	static #staticMap = {
    aa: 1,
    bb: 2
  }
}

// 正则 //d
const re1 = /a+(?<Z>z)?/d;
// ES 规范对 RegExp.prototype.exec（），String.prototype.match 等的结果附加了一个 indices 属性。
// indices.groups 也是区别 groups，并非返回匹配的字符串，而是返回匹配的字符串所在的 index 数组

// Top-level await
// 最外层直接使用 await

// in
function createClass() {
  return class {
    #name;
    static getName(inst) {
      if (#name in inst) {
          return inst.#name;
      }
      return undefined;
    }
  };
}

// Array.prototype.at
[1, 2, 3].at(-1) // 3

// hasOwn -> hasOwnProperty 替换品
if (Object.hasOwn(object, "foo")) {
  console.log("has property foo")
}
```



## 参考

- https://juejin.cn/post/6844903811622912014
- https://juejin.cn/post/6968113844086374431
- https://juejin.cn/post/6986087239554072583