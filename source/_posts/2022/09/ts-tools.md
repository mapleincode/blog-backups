---
title: TypeScript 的高级类型
date: 2022-09-27 15:32:25
tags: [typescript]
---



## Record

给若干个参数创建属性

```typescript
type Coord = Record<'x' | 'y', number>;

// 等同于
type Coord = {
	x: number;
	y: number;
}
```

## Pick

选择若干个参数

```typescript
type Coord = {
	x: number;
	y: number;
}

type CoordX = Pick<Coord, x>

type CoordX = {
 	x: number;
}

```





## Readonly

只读

```typescript
type Coord = {
	x: number;
	y: number;
}

type CoordX = Readonly<Coord>

type CoordX = {
 	readonly x: number;
  readonly y: number;
}
```

## Partial

参数可选。

```typescript
type Coord = {
	x: number;
	y: number;
}

type CoordX = Partial<Coord>

type CoordX = {
 	x?: number;
  y?: number;
}
```



## Required

与 Partial 相反

```typescript
type Coord = {
	x?: number;
	y?: number;
}

type CoordX = Required<Coord>

type CoordX = {
 	x: number;
  y: number;
}
```



## Exclude

```typescript
type T0 = Exclude<'a' | 'b' | 'c', 'b'> // 'a' | 'c'
```



## Extract

```typescript
type T0 = Exclude<'a' | 'b' | 'c', 'b' | 'd'> // 'b'
```



## Omit

**type** **Omit<T**, **K** **extends** **keyof** **any>** = **Pick**<**T**, **Exclude**<keyof **T**, **K**>>;



```typescript
interface I1 {
	a: number;
	b: string;
	c: boolean;
}

type AC = Omit<I1, 'b'>;     // { a:number; c:boolean } 
type C = Omit<I1, 'a' |'b'>  // { c: boolean }
```



## NonNullable

```typescript
type T1 = NonNullable<string | null | undefined>; // string
```



## Parameters

```typescript
type F1 = (a: string, b: number) => void;

type F1ParamTypes = Parameters(F1);  // [string, number]
```



## ConstructorParameters



```typescript
interface IEntity {
    count?: () => number
}

interface IEntityConstructor {
    new (a: boolean, b: string): IEntity;
}

class Entity implements IEntity {
    constructor(a: boolean, b: string) { }
}

type EntityConstructorParamType = ConstructorParameters<IEntityConstructor>; // [boolean, string]

function createEntity(ctor: IEntityConstructor, ...arg: EntityConstructorParamType): IEntity {
    return new ctor(...arg);
}

const entity = createEntity(Entity, true, 'a');
```



## ReturnType

返回函数返回的对象的类型

```typescript
type F1 = () => Date;

type F1ReturnType = ReturnType<F1>; // Date
```



## InstanceType

返回 new 类型的 interface 返回的对象。

```typescript 
type EntityType = InstanceType<IEntityConstructor>; // IEntity
```



## ThisParameterType

对象中 this 的类型。

```typescript
interface Foo {
    x: number
};

function fn(this: Foo) {}

type Test = ThisParameterType<typeof fn>; // Foo
```



## OmitThisParameter

移除 `this` 的数据类型，函数中无法使用 this。

```typescript
interface Foo {
    x: number
};

type Fn = (this: Foo) => void

type NonReturnFn = OmitThisParameter<Fn>; // () => void
```



## 表格

| 方法                                                       | 实现                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| Partial\<T>                                                | type Partial\<T> = {    [P in keyof T]?: T[P]; };            |
| Readonly\<T>                                               | type Readonly\<T> = {    readonly [P in keyof T]: T[P]; };   |
| Pick<T, K extends keyof T>                                 | type Pick<T, K extends keyof T> = {    [P in K]: T[P]; };    |
| Record<K extends keyof any, T>                             | type Record<K extends keyof any, T> = {    [P in K]: T; };   |
| Required< T >                                              | type Required\<T> = {    [P in keyof T]-?: T[P]; };          |
| Exclude<T, U>                                              | type Exclude<T, U> = T extends U ? never : T;                |
| Extract<T, U>                                              | type Extract<T, U> = T extends U ? T : never;                |
| Omit<T, K extends keyof any>                               | type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>; |
| NonNullable< T >                                           | type NonNullable\<T> = T extends null                        |
| Parameters<T extends (...args: any) => any>                | type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never; |
| ConstructorParameters<T extends new (...args: any) => any> | type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never; |
| ReturnType<T extends (...args: any) => any>                | type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any; |
| InstanceType<T extends new (...args: any) => any>          | type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any; |
| ThisParameterType< T >                                     | type ThisParameterType\<T> = T extends (this: infer U, ...args: any[]) => any ? U : unknown; |
| OmitThisParameter< T >                                     | type OmitThisParameter\<T> = unknown extends ThisParameterType\<T> ? T : T extends (...args: infer A) => infer R ? (...args: A) => R : T; |



## 参考

- https://juejin.cn/post/6844904066489778183
- https://juejin.cn/post/6844904068096196621