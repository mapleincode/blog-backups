---
title: TypeScript 重载
tags: [typescript]
date: 2023-04-20 19:37:01
---



函数重载

```typescript
// 声明和实现写在一起

function t1(p: string): number;
function t1(p: number): string;
function t1(p: string | number) {
    return typeof p === 'string' ? Number(p) : String(p);
}
```





类方法重载和函数重载其实是一样的。

是需要在类的方法上面，写上声明。

> 之前查询说必须要基于类的声明实现，其实是不需要的。
>
> 不过如果是基于 interface 实现类，那相当于把 interface 实现的两个方法复制到类里面来。

```typescript
class TestClass {
  // 在方法上面重写一次声明
  t1 (p: string): number;
  t1 (p: number): string;
  t1 (p: string | number): number | string {
    return typeof p === "string" ? Number(p) : String(p);
  }
}

const tt = new TestClass();
const t1 = tt.t1(1); // typeof t1 == "string" 
const t2 = tt.t1("1"); // typeof t2 == "number"
```



参考：

- https://www.jianshu.com/p/f86fe0b5ea5a
