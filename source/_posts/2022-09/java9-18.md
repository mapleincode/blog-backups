---
date: 2022-09-27 17:08:37
title: Java9 - Java18 新特性
tags: [java]
---



## Java9

1. jshell
2. 接口私有方法
3. 优化了 try with resource
4. 不支持 _ 作为变量名
5. String 内部从 char 存储改为 byte 存储
6. 模块分组
7. Stream 加强
8. 集合加强
   1. List.of()
   2. Map.of()
   3. copyOf()




## Java10

1. 用了 var 来进行自动推导类型



## Java 11 (LTS)

1. String.strip 用来去除首尾空格，还能除去 unicode 编码的空白字符
2. String.isBlank 判断字符串是否为 0 或者空格等空白字符
3. String.repeat(n) 字符串重复
4. Optional 加强
5. InputStream.t5ransferTo
6. HTTP Client API 正式版



## Java 12

1. switch 优化 `case 3,4,5 -> System.out.println("spring");`



## Java 13

1. switch 支持返回值

2. 支持 `""" """` 来创建字符串，支持分行  (python 以前的特性)



## Java 14

1. instanceof 模式匹配

2. NullPointerException 更好的 null 提示

3. record 类型
   1. 状态描述中的每个组件都有对应的private final字段。
   2. 状态描述中的每个组件都有对应的public访问方法。方法的名称与组件名称相同。
   3. 一个包含全部组件的公开构造器，用来初始化对应组件。
   4. 实现了equals()和hashCode()方法。equals()要求全部组件都必须相等。
   5. 实现了toString()，输出全部组件的信息。




## Java 15

1. Sealed Classes
   1. 如果指定模块的话，sealed class 和其子类必须在同一个模块下。如果没有指定模块，则需要在同一个包下。
   2. sealed class 指定的子类必须直接继承该sealed class。
   3. sealed class 的子类要用 final 修饰。
   4. sealed class 的子类如果不想用 final 修饰的话，可以将子类声明为sealed class。
2. CharSequence 新增了 isEmpty() 方法
3. TreeMap 新增方法
   1. putIfAbsent
   2. computeIfAbsent
   3. computeIfPresent
   4. compute
   5. merge
4. """ 成为正式功能



## Java 16

1. 包装类构造方法的警告
2. 新增日时段
3. InvocationHandler.invokeDefault
4. Stream().toList()



## Java 17 (LTS)

1. switch 优化
2. Sealed Classes 正式启用
3. 伪随机数的变化，增加了伪随机相关的类和接口
   1. RandomGenerator
   2. RandomGeneratorFactory
4. 去掉了 AOT 和 JIT
   1. AOT（Ahead-of-Time）
   2. GRaal



## Java 18

1. 默认使用 UTF-8
   1. -Dfile.encoding=UTF-8 
2. 支持简单的 web 服务器
3. 标记将要废弃的方法
   1. Object.finalize
   2. Thread.stop
4. @snippet 注解



