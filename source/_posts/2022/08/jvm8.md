---
title: JVM 8 特有的参数（区别 JVM 7）
date: 2022-08-11 15:05:00
tags: java
---



## JVM 内存模型

- 虚拟机栈
  - 每个线程都有私有的栈
  - 栈帧
    - 每个方法会创建一个栈帧
    - 包含
      - 局部变量表
      - 操作数栈
      - 方法出口
      - ...
    - StackOverflowError
- 本地方法栈
  - 虚拟机的 Native 方法所用的栈
- PC 寄存器
  - 线程的程序计数器
- 堆
  - 堆内存
  - 所有线程共享部分
  - 包含所有对象和数组（所有指针型变量？）
  - GC 的对象
- 方法区
  - 物理位置也属于堆
  - 包含
    - 存储类的信息
    - 常量池
    - 方法数据
    - 方法代码



## PermGen 永久代

- jdk 1.7 HotSpot 特有，jdk 1.8 移除
- 方法区的具体实现



> jdk 7 中，存储在永久代的部分数据就已经转移到了Java Heap或者是 Native Heap。

## Metaspace 元空间

代替 PermGen的， jdk 8 的实现



## 元空间

| 参数名称             | 含义       | 默认值 |          |
| -------------------- | ---------- | ------ | -------- |
| -XX:MetaspaceSize    | 元空间大小 |        | Jdk8版本 |
| -XX:MaxMetaspaceSize | 最大元空间 |        | Jdk8版本 |







## 参考

- https://www.cnblogs.com/paddix/p/5309550.html