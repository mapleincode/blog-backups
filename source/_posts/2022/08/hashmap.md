---
title: hash map
date: 2022-08-19 11:39:42
tags: []
---



Hash Map

- 原理
  - 散列 （hash）
    - 把 key 转化为数字哈希，再把数据存入数组，实现 O(1) 查询
- 冲突
  - 构造理想的 Hash 函数
  - 提供冲突解决方案
    - 开放寻址法
      - 线性查探
        - Linear Probing
        - 查询下一个位置是否为空，为空直接插入。
        - 查询 key 如果被占用就查询下一个。
        - 哈希表比较慢会影响性能，多次查询
      - 二次查探
      - Quadratic Probing
        - 由线性查探 i 步长，改为 i^2 步长
      - 双三列
        - Double Hash
        - 两个或以上的 Hash 函数
    - 链表法
      - 利用链表去解决重复 hash 的问题
      - 链表长，转为红黑树
        - 红黑树节点少，转为链表
- 缺点
  - 本身存储空间相对于数组而言，会大不少
    - 包含了链表、红黑树
  - 以为涉及链表、红黑树的读写会造成性能问题，所以尽量可以初始化比较大的哈希表，这样造成更大的空间浪费
  - 哈希表本质就是一个数组
    - 那么查询方式基于二分法
      - 数据分布的均匀情况会影响查询效率
    - 如果哈希表较大，而数据又比较少，那本身的查询效率依然会偏低