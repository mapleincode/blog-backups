---
title: Java Stream
date: 2022-08-01 16:34:17
tags: [java]
---

# 5. 实验结果总结

从以上的实验来看，可以总结处以下几点：

- 在少低数据量的处理场景中（size <= 1000），stream 的处理效率是不如传统的 iterator 外部迭代器处理速度快的，但是实际上这些处理任务本身运行时间都低于毫秒，这点效率的差距对普通业务几乎没有影响，反而 stream 可以使得代码更加简洁；
- 在大数据量（szie > 10000）时，stream 的处理效率会高于 iterator，特别是使用了并行流，在cpu恰好将线程分配到多个核心的条件下（当然parallel stream 底层使用的是 JVM 的 ForkJoinPool，这东西分配线程本身就很玄学），可以达到一个很高的运行效率，然而实际普通业务一般不会有需要迭代高于10000次的计算；
- Parallel Stream 受引 CPU 环境影响很大，当没分配到多个cpu核心时，加上引用 forkJoinPool 的开销，运行效率可能还不如普通的 Stream；

# 6. 使用 Stream 的建议

- 简单的迭代逻辑，可以直接使用 iterator，对于有多步处理的迭代逻辑，可以使用 stream，损失一点几乎没有的效率，换来代码的高可读性是值得的；
- 单核 cpu 环境，不推荐使用 parallel stream，在多核 cpu 且有大数据量的条件下，推荐使用 paralle stream；
- **stream 中含有装箱类型，在进行中间操作之前，最好转成对应的数值流，减少由于频繁的拆箱、装箱造成的性能损失。**

 

from:

https://www.infoq.cn/article/6521ea3ae8a36f2bdaece6e61