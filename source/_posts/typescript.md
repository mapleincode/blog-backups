---
title: TypeScript 学习笔记
date: 2018-07-03 18:01:01
tags: [ TypeScript ]
---

## 基础类型

[https://www.tslang.cn/docs/handbook/basic-types.html]()

### 布尔值

```typescript
let isDone: boolean = false;
```

### 数字

```typescript
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d;
let binaryLiteral: number = 0b1010;
let octalLiteral: number = 0o744;
```

### 字符串

```typescript
let name: string = "bob";
```

### 数组

```typescript
let list: number[] = [1, 2, 3];
let list: Array<number> = [1, 2, 3];
```

### 元组 Tuple

```typescript
let x: [string, numbr] = ['one', 1];
```

> 访问越界元素，会使用联合类型。

### 枚举

```typescript
enum Color { Red, Green, Blue }
let c: Color = Color.Green;
```

默认 0 开始，可以设置 1 开始编号。

```typescript
enum Color { Red = 1, Green, Blue }
```

也可以全部手动赋值。

### Any

```typescript
let notSure: any = 5;
noSure = false;
noSure = "hello world";
```

### Void

只能赋值 null 和 undefined

```typescript
function warnUser(): void {
    alert("hello warn User");
}
let unustable: void = undefined;
unustable = null;
```

### Null 和 Undefined

```typescript
let u: undefined = undefined;
let n: null = null;
```

默认情况下`null`和`undefined`是所有类型的子类型。 就是说你可以把 `null`和`undefined`赋值给`number`类型的变量。

然而，当你指定了`--strictNullChecks`标记，`null`和`undefined`只能赋值给`void`和它们各自。

也许在某处你想传入一个 `string`或`null`或`undefined`，你可以使用联合类型`string | null | undefined`。

推荐 `--strictNullChecks`

### Never

`never`类型表示的是那些永不存在的值的类型。

```typescript
// 返回never的函数必须存在无法达到的终点
function error(message: string): never {
    throw new Error(message);
}
```

### 类型断言

```typescript
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
```

```typescript
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;
```

> JSX 仅支持 as

## 变量声明

### 变量申明

跳过

### `var`声明

```typescript
var a: number = 10;
```

### `let`声明

```typescript
let a: number = 10;
```

### `const`声明

```typescript
const numLivesForCat: number = 9;
```

### 解构

```typescript
const input = [1, 2];
const [first, second] = input;
const q = { a: 'aa' };
const { a } = q;
```

算了，这个看过 ES6 的都懂。



## 接口

### 接口初探

```typescript
interface LabelledValue {
    label: string; // 必须属性
    color?: string; // 可选属性
    readonly size: number; // 只读属性。只能创建时修改。
}

function printLabel(labelledObj: LabelledValue) {
    console.log(labelledObj.label);
}
const myObj = { size: 10, label: "Hello World" };
```

#### `ReadOnlyArray<T>` 只读数组

```typescript
let a: number[] = [1, 2, 3, 4];
let ro: ReadonlyArray<number> = a;
ro[0] = 12; // error!
ro.push(5); // error!
ro.length = 100; // error!
a = ro; // error!
```

#### 额外属性检查

```typescript
interface SquareConfig {
    color?: string;
    width?: number;
    [propName: string]: any; // 索引签名
}
function createSquare(config: SquareConfig) {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 }); // error
let mySquare = createSquare({ colour: "red", width: 100 } as SquareConfig); // 属性断言来强制结束检查

// 定义另一个对象就不会进行属性检查。好坑啊。
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```



### 函数类型

接口可以描述函数类型。

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc = function(src: string, sub: string): boolean {
    let restult = src.search(sub);
    return result > -1
}
```

### 可索引的类型


