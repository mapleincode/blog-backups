---
title: TypeScript Type 类型判断
date: 2023-04-21 16:33:03
tags: [typescript]
---



TS 中官方支持的方法里，判断 Type 类型的有：

1. typeof ，但是遗憾的只支持基础类型
2. instanceof，只支持类



也就是混合的自定义 Type 而言，官方是没有常见的函数来进行判断。不过 TS 也给了办法。



先说怎么判断，TS 的类是通过参数字段来判断是否是同一个类型的 Type。也是满足字段类型相同的变量，就可以认为是属于某一个 Type。

所以可以通过 `in` 来判断是否包含某个字段。



## 初始

先定义一些类型：

```typescript
interface A {
  a: string
}
interface B {
  a: number
  b: string
}
interface C {
  c: string
}

type ObjectType = A | B | C
```



## 使用 in 判断

从搜索引擎出来的结果来看，很多人选择使用 in 来判断对象是否含有参数，比如：

```typescript
function test (o: ObjectType): void {
  if ("c" in o) {
    console.log(o.c); // type o == C
  }
}
```

但是通过 in 只能判断对象是否含有这个字段。例如 `o.a`， A 和 B 类型都有这个字段，并且字段类型不一样。

这时候就需要通过判断 `o.a` 来判断数据的类型

```typescript
function test (o: ObjectType): void {
  if ("a" in o && typeof o.a === "string") {
    console.log(o.a.length); // type o == A
  }
}
```



**也就是 in 是检测类型归属的方法，而不是唯一方法。**





## 封装成函数

虽然但是，通过 in 的方式可以满足绝大部分的场景。对于频繁判断类型的场景，我们需要把 in 封装成函数，这样代码可读性也提高了。

```typescript
function testTypeA (o: ObjectType): boolean {
  return "a" in o;
}
```

但是实际上，testTypeA 并不能用来判断，原因大概是被封装在函数中，ts 无法检测这个判断能过滤 Type。

但是 ts 会填坑啊，于是有了 `is` 字段。

最后得到了一个方法：

```typescript
function testTypeA (o: ObjectType): o is A {
  return "a" in o;
}
```



## 通用函数

搞成泛型！

```typescript
export function testTypeX<D extends Object, E extends D> (s: D, field: string): s is E {
  return field in s;
}
```



1. D extends Object，因为 in 方法只支持 Object
2. E extends D。因为判断 D 类型的变量是 A，那么 D 一定包含 A 的类型。



> Type 里的  extends 容易和面向对象的 extends 搞混。虽然两者从数据结构的定义来说是一样的。
>
> Type D = A | B | C；
>
> 相当于 D 是 A，B，C 的交集，也就是 D 是 A，B，C 的交集。
>
> 而 BaseClass 和 SuperClass 的关系，也是 BaseClass 是 SuperClass 的子集。
>
> 我之前一直理解不了，D 明明拥有 A，B，C 所有字段。实际上 D 只是从定义上拥有，如果只是 D 类型的变量，只能使用 A，B，C 交集的字段。
>
> 只有当判定 D 变量属于某个 type 的时候，才能使用这个 type 的属性。
>
> 例如通过 testTypeX 判定变量 s 属于 A type 时，其实 s 就是 A type，理所当然拥有 `s.a` 参数。
>
> 而超类和子类也是差不多的关系。子类拥有被超类更多的字段。



## 扩展多字段

前面说了，in 的方法只是用来自定义判断字段类型。对于复杂结构类型，就需要进行多字段判断。

```typescript
// eslint-disable-next-line @typescript-eslint/ban-types
export function testType<D extends Object, E extends D> (s: D, fields: string[] | string): s is E {
  if (typeof fields === "string") {
    fields = [fields];
  }

  return fields.every(field => field in s);
}
```



## 扩展数组类型判断

> 其实这个比较无用，但是既然写了就放上来

```typescript
// eslint-disable-next-line @typescript-eslint/ban-types
export function testTypeList<D extends Object, E extends D> (items: D[], fields: string | string[]): items is E[] {
  if (items.length === 0) {
    return true;
  }

  if (!Array.isArray(fields)) {
    fields = [fields];
  }

  for (const item of items) {
    const test = fields.every(field => field in item);
    if (!test) {
      return false;
    }
  }
  return true;
}
```

> 其实本来想通过两个 every 内嵌实现判断，结果 fields 字段出现 bug ，在第二层 every 方法中，无法确定 Array.isArray 方法的判断，无法转义，导致识别成 fields 可能为字符串。





## 在 interface 里定义字段来区分

这个也是我想到一个比较笨的方法。

```typescript
interface A {
  type: "A"
  a: string
}
interface B {
  type: "B"
  a: number
  b: string
}
interface C {
  type: "C"
  c: string
}
type ObjectType = A | B | C
function test (o: ObjectType): void {
  if (o.type === "A") {
    console.log(o.a.length); // type o == A
  }
}
```

当然这也是说明，`in` 只是判断 Type 类型的手段之一，而不是唯一的方法。只不过 `in` 方法适用于任何 Object 类型的 Type，并且不需要额外添加字段。



## 衍生

理论上可以无限的通过配置的方式来解析字段和类型是否相符合。但是这么做其实还是有点本末倒置了。用最简单的方法来区分混合 Type，或者尽量避免混合参数的情况才是正解，或者可以封装成 class 对象。



## 参考

- https://segmentfault.com/q/1010000039688789