---
title: Java 的一系列概念
date: 2022-08-11 16:43:33
tags: [java]
---



## Java

一门编程语言，这个没啥好说的。



## JVM

Java Virtual Machine 虚拟机

例如：

- Sun Classic VM 
  - JDK 1.0 提供的虚拟机
- Exact VM
  - JDK 1.2 引入，被 HotSpot 替代
- HotSpot VM
  - JDK 1.2 引入
  - JDK 1.3 之后默认的虚拟机
  - OpenSDK 的默认虚拟机
- JRockit
  - BEA 公司，被 Oracle 收购
  - 起特性被引入到 OpenSDK 所使用的 HotSpot 
  - 可以说现在 OpenSDK 使用的 jvm 其实是两者的合体
- J9
  - IBM 家的虚拟机



## JRE

> Java Runtime Environment

Java 运行环境。包含 JVM。



## JDK

> Java Development Kit

Java 的开发工具包。包含了 JRE，并且增加了例如类库源码、编译工具、调试工具等用于开发的内容。



> JDK 和 JRE 运行性能没有区别，只是文件数量和大小有区别



## Java SE

Java Standard Edition，Java 标准版。

Java SE 本质是一套应用，所以也可以认为就是 JDK。



## OpenSDK

2006 年 Sun 公司宣布开放 Java 源代码。

要知道开源并非是把 Java SE 闭源代码公布出来这么简单，Java SE 的实现有不少包含其他项目的闭源代码，甚至是专利的算法或者不公开的算法。

OpenSDK 就是 Sun 公司把闭源代码替换掉之后公布出来的开源版本的 Java SDK，使用 GPL 开源证书。

前期 Sun 公司主导开发，后期 Oracle 接手之后，干了几年，到发布 OpenSDK 8 之后，就甩手把项目给了 Eclipse 基金会。从此 OpenSDK 可以认为成为了一个受开源组织控制的开源项目。

所有基金会的成员都有权利选择 OpenSDK 的走向，这和 Oracle SDK 不一样。



## Oracle SDK

Oracle 收购 Sun 公司，本身就是为了赚钱，当然 Sun 公司也是因为 Java 不赚钱才被收购的（大雾）。

要知道 OpenSDK 虽然是 Java 开源，但是依然删除和修改了部分闭源代码。而且 Oracle 自身的研发能力也不差，于是他搞出了闭源版本的 SDK，也就是 Oracle SDK。同时他还修改了早期版本的 SDK 的证书。在 Sun 年代，SDK 是可以自由的被使用到开发，桌面甚至是服务器。而 Oracle 规定，Oracle SDK 都必须使用商业授权才允许商用。

这里包括 JDK8 也可以认为是 Oracle SDK （实际上 Jave SE 7 和 8 都是 Oracle 发布的）。只是 JDK 8 直到 2019 年 1 月停止商用的更新。**所以使用 JDK 8 2019 年 1 月之后的版本进行商业行为，都属于侵权，Oracle 是可以发起侵权控告的**。

> 最后能免费商用的版本，从时间节点来说是 2019 年 1 月 发布的 JDK 8 u191 版本。而 Oralce 发布声明称是 4 月之后才正式会开始。所以  u201 版本和 u202 版本依然不受影响，可以正常商用。
>
> 而 JDK 8u211 成为了第一个需要商业收费的 JDK 8 版本

JDK 8 本身会更新到 2030 年 12 月，但是他已经和其他版本的 Oracle SDK 一样，非商用使用是免费的，而商用是收费的。

但是从 JDK 9 开始，Oralce SDK 本身也是基于 OpenSDK 进行开发，也就是 OpenSDK 作为了 Oracle SDK 的测试版本。就和 CentOS 与 Red Hat 企业版的关系一样。



## JVM 规范

Java 虚拟机规范。

OpenSDK 开源了，但是 OpenSDK 有些大厂觉得组件不行，对其组件进行更新，甚至某些更新可能是闭源的（比如 Oracle SDK）。那么就需要一套规范，去规定同个 Java 版本下的 jvm 的需要实现什么功能等等。也就是 JVM 规范了。

例如 IBM 的 J9，也是通过 JVM 规范实现的 jvm 虚拟机，OpenSDK 和 Oracle SDK 同样也是同一套规范下的结果。虽然实现不一样，但是最终的结果必须是一样的。

> 有点像 ECMAScript 下实现的不同的 JavaScript 解释器一样。



## 参考

- https://zh.wikipedia.org/wiki/Java
- https://zh.m.wikipedia.org/wiki/OpenJDK
- https://juejin.cn/post/6844903811069247496
- https://blog.csdn.net/ancientear/article/details/79483592
- https://www.zhihu.com/question/31455874
- https://zh.m.wikipedia.org/wiki/Java%E7%89%88%E6%9C%AC%E6%AD%B7%E5%8F%B2
- https://blog.csdn.net/lywstuding/article/details/121117136