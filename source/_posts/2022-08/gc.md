---
title: Java GC
date: 2022-08-10 17:15:45
tags: [java]
---

- Serial GC

  - 单线程收集器

  - 需要暂停其他工作线

  - Client 模式

- Serial Old GC
  - Serial 老年代版本，Client 版本
    - 其他功能
      - 搭配Parallel Scavenge收集器使用。
      - 做为 CMS 收集器的后备方案，当 CMS 出现 Concurrent Mode Failure 时使用。
      - 做为 G1 的 fullGC 收集器。这也是我们在使用 G1 的时候需要注意的，G1 应该尽量杜绝FullGC。否则就会退化为串行 GC。
  
- ParNew GC
  - Serial 收集器的多线程版本，其他参数方式都一样
  - XX:parallelGCThreads 设置线程数
  
- Parallel Scavenge收集器
  - 复制算法
  - 负责新生代的收集
  
- Parallel Old GC
  - Parallel Old 收集器是 Parallel Scavenge 收集器的老年代版本
  - 多线程的标记整理算法
  
- STW GC
  - stop world GC
  
  - 区别于 CMS 等之后的 GC
  
  - 实质上就是 Serial GC 和 Parallel GC 等老一代 GC 的统称
  
  - 特点是会造成 STW
  
- CMS GC
  - 算法复杂，调优参数多，调优难
  - GC 时间短，适合互联网服务器
  - 对 CPU 资源敏感
  - 无法处理浮动垃圾，如果浮动垃圾比较大会造成 GC 失败，从而触发 SerialOld GC
  - JDK 9 废弃

- G1 GC
  - Java 7 开始引入

  - Java 9 开始默认的 GC 算法，是 CMS 的升级

  - 将 heap 内存分成相等的 Region，每个 Region 范围是 1M-32M，减少因为 GC 区域过大造成的耗时

  - XX:G1HeapRegionSize

  - CSet

    - GC 过程记录可被回收的 Region

  - Rset

    - 记录其他 Region 引用本 Region 的关系

  - Humongous regions

    - 存放大于标准 Region 内存 50% 的大对象区域
    - 如果大于 100% 选择连续的 Region 存储
    - GC 快，但是可能会占用最多 200% 的内存

  - 回收的模式

    - 默认 Young GC 和 Mixed GC ，不行了就 Full GC。

    - Young GC
      - 所有年轻代
    - Mixed GC
      - 所有年轻代 + 并发标记阶段收益搞的老年代
    - Full GC
      - 使用 Serial 收集器
      - 对象迭代速度过高导致 G1 回收失败
      - 尽量避免

- Z GC
  - 可扩展的低延迟垃圾回收期
  - Java 11 12 提供早期版本
  - 设计目标
    - 停顿时间不超过 10ms
      - 美图风控服务使用 CMS，GC 时间在 40ms ，10次 / 分钟
    - 停顿时间不会随着堆的大小、或者活跃对象的大小而增加
    - 支持 8MB~4TB 级别的堆（未来支持 16TB）
  - **ZGC在标记、转移和重定位阶段几乎都是并发的**
  - 读屏障 技术
    - 着色指针
    - 并发标记对象，一旦对象移动地址，触发该逻辑
    - 优化
      - **-Xms -Xmx**：堆的最大内存和最小内存，这里都设置为10G，程序的堆内存将保持10G不变。
      -  **-XX:ReservedCodeCacheSize -XX:InitialCodeCacheSize**：设置CodeCache的大小， JIT编译的代码都放在CodeCache中，一般服务64m或128m就已经足够。我们的服务因为有一定特殊性，所以设置的较大，后面会详细介绍。
      -  **-XX:+UnlockExperimentalVMOptions -XX:+UseZGC**：启用ZGC的配置。 
      - **-XX:ConcGCThreads**：并发回收垃圾的线程。默认是总核数的12.5%，8核CPU默认是1。调大后GC变快，但会占用程序运行时的CPU资源，吞吐会受到影响。
      -  **-XX:ParallelGCThreads**：STW阶段使用线程数，默认是总核数的60%。
      -  **-XX:ZCollectionInterval**：ZGC发生的最小时间间隔，单位秒。 
      - **-XX:ZAllocationSpikeTolerance**：ZGC触发自适应算法的修正系数，默认2，数值越大，越早的触发ZGC。 
      - **-XX:+UnlockDiagnosticVMOptions -XX:-ZProactive**：是否启用主动回收，默认开启，这里的配置表示关闭。
      -  **-Xlog**：设置GC日志中的内容、格式、位置以及每个日志的大小。
- Shenandoah GC
  -  Red Hat 开发
  - Shenandoah GC 也会造成 STW 停顿，但通常都很短暂，因为它是在应用程序运行的同时执行大量的 GC。
  - 这种停顿不会随着堆的增大而延长
  - 参数 heuristic
    - adaptive
      - 根据程序启动前几分钟对象的分配速速来推断 GC 的阈值

    - static
      - 指定在剩余多少可用内存时触发 GC

    - compact
      - 不会发生步调调整或进入退化模式

  - 弱引用会增加停顿的时间
    - 包括内存泄漏检测机制，因为其使用了 finalizer

  - 由反射调动引起的类加载器会造成内存泄漏，回收期不会卸载这些类
    - 通过设置-Dsun.reflect.inflationThreshold=2147483647 来临时规避

  - Shenandoah 会通过 CPU 数来决定线程数
    - 需要保证有足够多的 CPU 数



## 参考

- https://www.jianshu.com/p/5d80e3f401bd
- https://cloud.tencent.com/developer/article/1519931
- https://tech.meituan.com/2020/08/06/new-zgc-practice-in-meituan.html
- https://www.infoq.cn/article/l4lu1j0vxxta6asegybv