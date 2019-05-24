---
title: TypeScript 学习笔记
date: 2018-07-03 18:01:01
tags: [ TypeScript ]
---

> 首先要弄清楚 TypeScript 可以做什么。
>
> 在一个项目逐渐庞大，各种 API 和函数层出不穷，往往传错一个参数短时间并不会影响什么，但是时间一久就会引发灾难性的问题。
>
> TypeScript 通过在编译时检查其 Type 来避免类似参数传入错误的问题。同时通过限制参数的类型（包括 类、函数）的结构，避免参数乱传的问题。
>
> TypeScript 手册相对于其他语言来说完全算少，尤其是对于我们这种 ES6 已经写了大半年的人来说。
>
> 所以更多的是怎么快速的掌握和记住一些 TypeScript 的细节。
>
> 看完手册已经差不多一个月了，这次作为复习也作为笔记。



## 基础类型

1. 支持 const、let

2. `let 变量: type = value`

3. boolean, number, string, number[], Array\<number>, [string, number], any, enum

4. `enum Color = { Red: 1, Green, Blue }`

5. Object

6. void(undefined, null)

   ```
   function warnUser(): void;
   ```

7. undefined, null

8. never

   ```js
   function error(message: string): never {
       throw new Error(message);
   }
   ```

9. declare 声明

   ```typescript
   declare function create(o: object): void;
   
   function create(o: object) {
       return;
   }
   ```

10. `let a = <number>b`

11. `let a = b as number`

## 变量声明

1. `let`
2. `const`
3. `const [a, b]: = [1, 2];`
4. `const { a: c, b } = { a: 1, b: 2 };`
5. `type C = { a: string, b?: number }`
6. const newList = [ ...oldList ];

