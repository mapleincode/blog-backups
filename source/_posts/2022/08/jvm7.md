---
title: jvm7 一份调优参数分析
date: 2022-08-09 18:59:54
tags: [java, jvm]
---

前几天看文章，看到了一篇文章给了一份 jvm7 的启动调优参数。可以学习下 JVM7 的一些特性。



```shell
java -server -Xms2G -Xmx2G -XX:MaxPermSize=256M -Xss256K -XX:NewRatio=2 -XX:SurvivorRatio=8 -XX:+UseAdaptiveSizePolicy -XX:+UseParallelGC -XX:+UseParallelOldGC -XX:+PrintGCDetails -XX:+PrintGCDateStamps -Xloggc:../logs/gc.log -XX:+PrintTenuringDistribution  -XX:+PrintHeapAtGC -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=../logs/dump.hprof -jar pphh.jar
```



```shell
$ java7 -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)

$ java7 -help
Usage: java [-options] class [args...]
           (to execute a class)
   or  java [-options] -jar jarfile [args...]
           (to execute a jar file)
where options include:
    -d32	  use a 32-bit data model if available
    -d64	  use a 64-bit data model if available
    -server	  to select the "server" VM
                  The default VM is server,
                  because you are running on a server-class machine.


    -cp <class search path of directories and zip/jar files>
    -classpath <class search path of directories and zip/jar files>
                  A : separated list of directories, JAR archives,
                  and ZIP archives to search for class files.
    -D<name>=<value>
                  set a system property
    -verbose:[class|gc|jni]
                  enable verbose output
    -version      print product version and exit
    -version:<value>
                  require the specified version to run
    -showversion  print product version and continue
    -jre-restrict-search | -no-jre-restrict-search
                  include/exclude user private JREs in the version search
    -? -help      print this help message
    -X            print help on non-standard options
    -ea[:<packagename>...|:<classname>]
    -enableassertions[:<packagename>...|:<classname>]
                  enable assertions with specified granularity
    -da[:<packagename>...|:<classname>]
    -disableassertions[:<packagename>...|:<classname>]
                  disable assertions with specified granularity
    -esa | -enablesystemassertions
                  enable system assertions
    -dsa | -disablesystemassertions
                  disable system assertions
    -agentlib:<libname>[=<options>]
                  load native agent library <libname>, e.g. -agentlib:hprof
                  see also, -agentlib:jdwp=help and -agentlib:hprof=help
    -agentpath:<pathname>[=<options>]
                  load native agent library by full pathname
    -javaagent:<jarpath>[=<options>]
                  load Java programming language agent, see java.lang.instrument
    -splash:<imagepath>
                  show splash screen with specified image
See http://www.oracle.com/technetwork/java/javase/documentation/index.html for more details.
 
```



[toc]

## -server

server 用于切换 jvm server 模式

client 模式

- 启动速度快
- 优化相对简单
- 默认 -Xms=1M -Xmx=64M

server 模式

- 启动速度慢
- 深度优化
- 默认 -Xms=128M -Xmx=1024M



自动判断条件： 2 个 CPU 核心和 2G 内存

## -Xms2G -Xmx2G

xms: 启动时分配的内存

xmx: 运行时能分配的最大内存

##  -XX:MaxPermSize=256M

持久代最大值，一般选物理内存的 1/4。

## -Xss256K

每个线程分配的堆栈大小

> 1.4 版本是 256K
>
> 1.5 版本以后默认  1M

调低此参数有助于生成更多的线程。

> 一般小的应用， 如果栈不是很深， 应该是128k够用的 大的应用建议使用256k。这个选项对性能影响比较大，需要严格的测试

## -XX:NewRatio=2 

置新生代和老年代的比值。如 2，表示新生代与老年代比值为 1:2，新生代占整个新生代老年代和的 1/3。

## -XX:SurvivorRatio=8 

新生代包括 Eden 和两个 Survivor 区（From Survivor区、To Survivor区）。
`-XX:SurvivorRatio=8`，表示 Survivor区与 Eden 区的比值是1：8。两个 Survivor 区（From Survivor区、To Survivor区）与Eden区的比值为2：8，一个Survivor区占整个新生代的1/10

## -XX:+UseAdaptiveSizePolicy 

自动选择新生代区大小和相应的 Survivor 区比例

> 设置此选项后,并行收集器会自动选择新生代区大小和相应的Survivor区比例,以达到目标系统规定的最低相应时间或者收集频率等,此值建议使用并行收集器时,一直打开.

## -XX:+UseParallelGC 

设置并行收集器。

- 设置串行收集器
  - -XX:+UseSerialGC
- 设置并行收集器
  - -XX:+UseParallelGC



- `-XX:+UseParalledlOldGC`
  - 设置并行老年代收集器
  - 默认开启



- `-XX:+UseConcMarkSweepGC` 
  - 并发标记扫描（CMS）垃圾收集器
  - cms
  - Java 9 废弃，java 14 删除
  - 废弃原因是因为算法过于复杂，参数过多，调优难
  - 其他算法
    - 



## -XX:+UseParallelOldGC 

- 设置并行老年代收集器
- 默认开启

## -XX:+PrintGCDetails 

 打印 GC 详情。

输出形式:

[GC [DefNew: 8614K->781K(9088K), 0.0123035 secs] 18250K->113543K(130112K), 0.0124633 secs]
[GC [DefNew: 8614K->8614K(9088K), 0.0000665 secs]
[Tenured: 112761K->10414K(121024K), 0.0433488 secs]
121376K->10414K(130112K), 0.0436268 secs]



## -XX:+PrintGCDateStamps 

答应 GC 日期时间戳



## -Xloggc:../logs/gc.log 

GC 日志



## -XX:+PrintTenuringDistribution 

新生代 GC 时，打印幸存区中对象的年龄分布。



## -XX:+PrintHeapAtGC 

每次一次GC后，都打印堆信息



## -XX:+HeapDumpOnOutOfMemoryError 

当 JVM 发生 OOM 时，自动生成 DUMP 文件。



## -XX:HeapDumpPath=../logs/dump.hprof 

DUMP 文件的路径



## -jar pphh.jar



## 参考

- https://blog.csdn.net/xiaojin21cen/article/details/79480208
- https://blog.csdn.net/RickyIT/article/details/53895060

- https://blog.csdn.net/weixin_39525617/article/details/113582222
