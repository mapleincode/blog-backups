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



## 1. 基础类型

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

## 2. 变量声明

1. `let`
2. `const`
3. `const [a, b]: = [1, 2];`
4. `const { a: c, b } = { a: 1, b: 2 };`
5. `type C = { a: string, b?: number }`
6. `const newList = [ ...oldList ];` 
7. `const [ a ] =  [ 1 ]`



## 3. 接口

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

7. 数组类型的 interface

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
     new (hour: number: minute?: number);
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

##  4. 类

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

## 5. 函数

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

## 6. 泛型

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

## 7. 枚举

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

  8. 外部枚举是用来引入其他非 ts 代码的外部包已存在的枚举

     ```ts
     declare enum Enum {
       A = 1,
       B,
       C = 2
     }
     ```

## 8. 类型推论

1. `(number|string|boolean)`

## 9. 类型兼容性

1. 兼容性只得是某个对象能否被赋值给另一个对象

2. 属性相同的 class 和 interface 可以兼容。

3. 函数比较的是参数

   ```ts
   let x = (a: number) => 0;
   let y = (b: number, s: string) => 0;
   
   y = x; // OK
   x = y; // Error
   ```

4. 可选参数和剩余参数在转换时不会产生错误

5. 类之对比实例成员，不对比静态成员和构造函数

6. private 和 protect 只允许赋值给父类。

7. 泛型在结构相同的情况下可以转换，但是前提是不包含属性不同的成员。如果两者都是 any 就不会有问题。

## 高级类型

### 交叉类型(Intersection Types)

1. 指的是某个对象拥有多个 type，属于多个 type 的成员。

2. `T & U`

3. 检查类型检查的是所拥有的 key。如果吧  A、B 的key 都复制到 C 上面。那么`typeof C == typeof A & typeof B`

4. demo

   ```ts
   function extend<T, U>(first: T, second: U): T & U {
       let result = <T & U>{};
       for (let id in first) {
           (<any>result)[id] = (<any>first)[id];
       }
       for (let id in second) {
           if (!result.hasOwnProperty(id)) {
               (<any>result)[id] = (<any>second)[id];
           }
       }
       return result;
   }
   ```

### 联合类型(Union Types)

1. `number | string`
2. 出参如果是联合类型，因为无法确定具体属于什么类型。导致只有两个类型共有部分的属性可以被使用。
3. 可以使用强制转换。

### 类型保护与区分类型(Type Guards and Differentiating Types)

1. 为了解决 **联合类型** 输出参数类型不确定问题

2. 类型断言

   ```ts
   let pet = getSmallPet();
   if ((<Fish>pet).swim) {
       (<Fish>pet).swim();
   }
   ```

3. 用户自定义的类型保护

   ```ts
   function isFish(pet: Fish|Bird): pet is Fish {
       return <Fish>pet).swim !== undefined;
   }
   
   if (isFish(pet)) {
       pet.swim();
   }
   ```

4. `typeof` 类型保护

   1. typeof 仅支持 `number`, `string`, `boolean`, `symbol`
   2. `if (typeof xx === 'string') { ... }`

5. `instanceof` 类型保护

   1. 此构造函数的 `prototype`属性的类型，如果它的类型不为 `any`的话
   2. 构造签名所返回的类型的联合

   > 上面两个感觉是机翻，没有例子也看不懂。

6. 可以为 null 的类型

   1. `null`与 `undefined`是所有其它类型的一个有效值。
   2. `--strictNullChecks`标记可以解决此错误

7. 类型别名

   1. type

   2. 不能出现在创建一个<声明>时，右侧有任何的声明

   3.   <我感觉一辈子都不会这么去用 orz>

      ```ts
      type LinkedList<T> = T & { next: LinkedList<T> };
      
      interface Person {
          name: string;
      }
      
      var people: LinkedList<Person>;
      var s = people.name;
      var s = people.next.name;
      var s = people.next.next.name;
      var s = people.next.next.next.name;
      ```

   4. type 和 interface 的区别

      1. type 更像是一个指针，在很多地方【错误】【提示】 等都会显示 type 原始的类型。
      2. interface 可以被 extends 和 implement 而 type 不行。

8. 字符串字面量类型

   1. `type Easing = "ease-in" | "ease-out" | "ease-in-out";`
   2. 字符串字面量类型还能区别与 string 而用于重载。

9. 数字子面型类型

   1. 同上
   2. 可以用来调试代码，缩小 number 范围带来的变量的报错。

10. 枚举成员类型

    参照 <枚举> 。

11. 可辨识联合(Discriminated Unions)

    1. <标签联合>、<代数数据联合>

    2. demo

       ```ts
       interface Square {
           kind: "square";
           size: number;
       }
       interface Rectangle {
           kind: "rectangle";
           width: number;
           height: number;
       }
       interface Circle {
           kind: "circle";
           radius: number;
       }
       
       type Shape = Square | Rectangle | Circle;
       
       function assertNever(x: never): never {
           throw new Error("Unexpected object: " + x);
       }
       function area(s: Shape) {
           switch (s.kind) {
               case "square": return s.size * s.size;
               case "rectangle": return s.height * s.width;
               case "circle": return Math.PI * s.radius ** 2;
               default: return assertNever(s); // error here if there are missing cases
           }
       }
       ```

12. 多态的 this 类型

    1. 菊花链的故事

13. 索引类型(Index Types)

    1. `let personProps: keyof Person; // 'name' | 'age'`

    2. demo

       ```ts
       function getProperty<T, K extends keyof T>(o: T, name: K): T[K] {
           return o[name]; // o[name] is of type T[K]
       }
       ```

14. 映射类型

    1. 可以支持把一个类型的 type 转换成另个类型的 type。

    2. 常用的几个类型

       ```ts
       // 把 type 所有类型都转成 ReadOnly
       type Readonly<T> = {
           readonly [P in keyof T]: T[P];
       }
       
       // 把 type 所有类型转成可选
       type Partial<T> = {
           [P in keyof T]?: T[P];
       }
       
       // 拆分 type 的若干个类型转换成一个新的 type
       type Pick<T, K extends keyof T> = {
           [P in K]: T[P];
       }
                 
       // 用 T 作为属性转成新的类型
       type Record<K extends string, T> = {
           [P in K]: T;
       }
       ```

    3. 包装 & 拆包

       ```ts
       // 包装
       type Proxy<T> = {
           get(): T;
           set(value: T): void;
       }
       type Proxify<T> = {
           [P in keyof T]: Proxy<T[P]>;
       }
       function proxify<T>(o: T): Proxify<T> {
          // ... wrap proxies ...
       }
       let proxyProps = proxify(props);
       // 拆包
       function unproxify<T>(t: Proxify<T>): T {
           let result = {} as T;
           for (const k in t) {
               result[k] = t[k].get();
           }
           return result;
       }
       
       let originalProps = unproxify(proxyProps);
       ```

    4. 实际包装完整代码(琢磨了半天)

       ```ts
       type Proxy<T> = {
           get(): T;
           set(value: T): void;
       }
       type Proxify<T> = {
           [P in keyof T]: Proxy<T[P]>;
       }
       
       function pro<T>(a: T): Proxy<T> {
           const b: Proxy<T> = {
               get() {
                   return a;
               },
               set(value: T) {
                   
               }
           }
           return b;
       }
       
       function proxify<T extends Object>(o: T): Proxify<T> {
           const p: Proxify<T> = {} as Proxify<T>;
           for(const key in o) {
               p[key] = pro(o[key]);
           }
           return p;
       }
       
       let proxyProps = proxify({ a: '2333'});
       ```

    5. 类似 `K extends keyof T`。因为 keyof 类似 Object.keys 返回的是复数的属性，而且在<> 也可以传入类似 `<'aa'|'bb'>`。所以在使用必须用 `[key in K]`。

    6. 预定义的有条件类型

       TypeScript 2.8在`lib.d.ts`里增加了一些预定义的有条件类型：

       - `Exclude<T, U>` -- 从`T`中剔除可以赋值给`U`的类型。
       - `Extract<T, U>` -- 提取`T`中可以赋值给`U`的类型。
       - `NonNullable<T>` -- 从`T`中剔除`null`和`undefined`。 
       - `ReturnType<T>` -- 获取函数返回值类型。
       - `InstanceType<T>` -- 获取构造函数类型的实例类型。

## Symbols

1. `let sym1 = Symbol(); let sym2 = Symbol("key");`
2. 还有很多内置的特性参照：<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol/asyncIterator>

## 迭代器 & 生成器

## 模块

## 命名空间

## 命名空间 & 模块

## 模块解析

## 声明合并

## JSX

## 装饰器

## Mixins

## 三斜线指令

## JavaScript 文件类型检查

---

## 其他

### 1. ts 代码中引入 js

### 3. 声明文件
### 4. 项目配置

