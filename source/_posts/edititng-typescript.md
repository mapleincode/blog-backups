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
6. `const newList = [ ...oldList ];` 
7. `const [ a ] =  [ 1 ]`



## 接口

1. interface

   ```js
   interface SquareConfig {
     readonly name: string;
     color: string;
     width: number;
   }
   
   const config: SquareConfig = {
     name: "Bob",
     color: "red",
     width: 300
   };
   ```

2. ReadonlyArray

   ```ts
   let ro: ReadOnlyArray<number> = a;
   ```

3. interface 和 type 的区别见<其他>

4. `createSquare({ xxx: "xxx" } as SquareConfig)`

5. propName

   - 适合在外部传入数据并且在不知道结构的情况下全全保存。

   ```ts
   interface U {
     [propName: string]: any
   }
   ```

6. 函数类型的 interface

   ```ts
   interface SearchFunc {
     (source: string, subString: string): boolean;
   }
   ```

7. 数组类型的 iunterface

   ```ts
   interface StringArray {
   	[index: number]: string;
   }
   ```

8. 索引

   1. 字符串 or 数字
   2. 数字的索引返回值的集合 必须是 字符串索引返回值的集合的子集。
   3. 索引可以限制所有子类型的返回值。 (type or readonly)

9. 类类型

   ```ts
   interface C {
     new (hour: number: minute?: number)
     time: Date;
     setTime(d: Date): boolean;
   }
   ```

10. 继承多个接口

    ```ts
       interface Square extends Shape, PenStroke {
           sideLength: number;
       }
    ```

    

11. 支持 对象和函数混合接口

12. 接口继承类

    ```ts
    class Button extends Control implements SelectableControl {
        select() { }
    }
    ```

##  类

1. `public`

2. `private`

3. `protected`

   1. `protected`修饰符与 `private`修饰符的行为很相似，但有一点不同， `protected`成员在派生类中仍然可以访问。

4. readonly

5. constructor 的参数支持直接赋值为 class 属性;<参数属性>;

   ```ts
   class F {
     constructor(public readonly f: string) {
       
     }
   }
   const f = new F("Bob");
   console.log(f.f);
   ```

6. get/set 支持<存取器>

   ```ts
   class G {
     private _fullName: string;
     get fullName() {
       return this._fullName;
     }
     
     set fullName(name: string) {
       this._fullName = name;
     }
   }
   ```

7. `static origin = { a: 1, b: 2 };`

8. 抽象类

   ```ts
   abstract class Department {
       abstract printMeeting(): void;
   }
   
   class AccountingDepartment extends Department {
       printMeeting() {
           console.log("printf");
       }
   }
   ```

9. 把<类>当做<接口>

   ```ts
   class Point {
       x: number;
       y: number;
   }
   interface Point3D extends Point {
       z: number;
   }
   ```

## 函数

1. 基本结构

   ```ts
   // 1
   function done(q: string): string {
       return q;
   }
   
   // 2
   const redone: (q: string) => string = function(q: string): string {
       return q;
   };
   
   // 3
   type Q = {
       (q: string): string;
   };
   
   const reredone: Q = function(q: string): string {
       return q;
   };
   
   // 4
   const rrrrdone: ({ (q: string): string; }) = function(q: string): string {
       return q;
   };
   
   // 5
   
   interface P {
       (q: string): string;
   }
   
   const rrrrrdone: P = function(q: string) {
       return q;
   }
   ```

2. 可选参数

   ```ts
   function buildName(firstName: string, lastName?: string): void {
       
   }
   ```

3. 支持默认参数 `function Q(q = "xxx") {}`

4. 剩余参数

   ```ts
   function buildName(firstName: string, ...restOfName: string[]) {
       
   }
   ```

5. 支持重载

## 泛型

1. demo

   ``` ts
   function identity<T>(arg: T): T {
       return arg;
   }
   const output = identity<string>("hello world");
   ```

2. multi

   ```ts
   function id<T, S>(argA: T, argB: S): S {
       
   }
   ```

3. interface

   ```ts
   interface A {
       <T>(arg: T): T;
   }
   ```

4. 泛型约束

   ``` ts
   interface LengthWish {
   	length: number;
   }
   
   function loggingIdentityZ<T extends LengthWise>(arg: T): T {
     console.log(arg.length);
     return arg;
   }
   ```



## 枚举

1. `enum Direction { Up=1, Down, Left, Right }`

2. 默认从 0 开始

3. 数字枚举 or 字符串枚举。

4. 异构枚举(Heterogeneous enums)

5. 反向映射

   ```ts
   enum Enum {
     A
   }
   
   let a = Enum.A;
   let nameofA = Enum[a]; // "A"
   ```

6. `const enum { A, B }`

7. 外部枚举

   ```ts
   declare enum Enum {
       A = 1,
       B,
       C = 2
   }
   ```

8. 

## 其他

### type 和 interface 的区别

