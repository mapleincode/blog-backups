---
title: Java 发展简史
date: 2022-08-11 18:29:25
tags: [java]
---

原链接：

https://blog.csdn.net/lywstuding/article/details/121117136

[toc]



## 1. C 语言，C++ 的诞生

> 这一段和 Java 毫无关系，感觉是作者拿来凑字数的，可以跳过。



Java 的诞生离不开 C 语言和 C++,C++ 是在 C 语言的基础上改造的，后来 Sun 公司又对 C++ 进行改写，产生了 Java。也可以说 Java 源自于 C 和 C++。



### 1956 年 FORTRAM 的诞生

1956 年，美国计算机科学家约翰・巴克斯带领团队开发出了世界上第一个高级编程语言 `FORTRAN`。

FORTRAN 语言是世界上第一个被正式推广使用的高级语言。它是 1954 年被提出来的，1956 年开始正式使用，直到 2022 年已有六十六年的历史，但仍历久不衰，**FORTRAN 始终是数值计算领域所使用的主要语言 **。



### 1958 年，ALGOL（ALGOrithmic Language）算法语言委员会小组成立

1958 年，ALGOL（ALGOrithmic Language）算法语言委员会小组成立，后续发表了 `ALGOL 系列算法语言`，对后来的高级语言（C、Pascal 等）产生了深远的影响。



- **1958 年，ALGOL 58 诞生 **
- **1960 年，ALGOL 60 诞生 **
- 1966 年，ALGOL W 出现，碍于调整与改进幅度略显保守，委员会并未以此作为 ALGOL 60 的后继者
- **1968 年，ALGOL 68 诞生 **



  ALGOL 是算法语言（ALGOrithmic Language）的简称，是在计算机发展史上首批清晰定义的高级语言，由欧美计算机学家合力所组成的联席大会于 1950 年代（晶体管计算机流行的年代）所开发。

  由于 ALGOL 语句和普通语言表达式接近，更适于数值计算，所以 ALGOL 多用于科学计算机。

  1950 年代末期，大量的新增计算机语言涌现，而这些新语言大都围绕单个体系结构如 UNIVAC 计算机、IBM700 系列计算机等开发，使不同系统用 户间的交流变得困难。

  1955 年，德国的应用数学和力学学会 (Gesellschaft für Angewandte Mathematik und Mechanik,GAMM) 因力拒 IBM 在计算机领域的主导地位，特设置一个专责于设计一通用但与计算机无关的算法语言的开发委员会。

  针对这种计算机专用语言大量增多情况，美国一些主要的计算机用户组，在 1957 年 5 月 10 日向国际计算机学会 (Association for Computing Machinery，ACM) 提交申请，建议研究与开发适用于与计算机无关的科学用程序设计语言。

  1957 年末，因在美国涌现多方计算机高级语言，GAMM 原小组委员会认为应该包揽美国同道中人而向 ACM 发出邀请，1958 年 4 月，两个小组正式同意加入这一联合语言设计项目。



### 1958 年 ALGOL 58 出现

  1958 年 5 月 27 日至 6 月 1 日，GAMM 和 ACM 各有 4 人出席在苏黎世举行第一次设计会议，为新语言定下目标。他们把关于算法表示法的建议综合为一，被命名为 IAL（国际代数语言，International Algebraic Language），后来改称 Algol 58。是 ALGOL 家族的第一个成员。



### 1960 年 ALGOL 60 出现

  图灵奖获得者：艾伦。佩利（Alan J.Perlis）在巴黎举行的有全世界一流软件专家参加的讨论会上，发表了 "算法语言 Algol 60 报告"，确定了程序设计语言 Algol 60。Algol60 语言的第一个编译器由 Edsger W. Dijkstra 来实现。

  Algol 60 引进了许多新的概念如：局部性概念、动态、递归、巴科斯 - 诺尔范式 BNF（Backus-Naur Form）等等。

  Algol 60 是程序设计语言发展史上的一个里程碑，它标志着程序设计语言成为一门独立的科学学科，并为后来软件自动化及软件可靠性的发展奠定了基础。

  ALGOL60 语言是计算机发展史上首批产生的高级程式语言家族



### 1963 年 CPL 语言（Combined Programming Langurage）诞生

  1963 年英国剑桥大学推出了 CPL（Combined Programming Langurage）语言，是基于 ALGOL 60 实现的高级语言。CPL 语言在 ALGOL 60 的基础上接近硬件一些，但规模比较大，难以实现。



### 1967 年 BCPL 语言（the Basic Combined Programming Language）诞生

1967 年英国剑桥大学的 Matin Richards 对 CPL 语言做了简化，推出了 BCPL （the Basic Combined Programming Language）语言。



### 1969 年 B 语言（The B Programming Language）诞生

  20 世纪 60 年代，美国 AT&T 公司贝尔实验室（AT&T Bell Laboratories）的研究员肯・汤普森（Kenneth Lane Thompson）闲来无事，手痒难耐，想玩一个他自己编的，模拟在太阳系航行的电子游戏 ——Space Travel。他背着老板，找到了台空闲的小型计算机 ——PDP-7。但这台电脑没有操作系统，而游戏必须使用操作系统的一些功能，于是他着手为 PDP-7 开发操作系统。后来，这个操作系统被命名为 ——UNICS（Uniplexed Information and Computing Service）。

  1969 年，美国贝尔实验室的肯・汤普森（Kenneth Lane Thompson），以 BCPL 语言为基础，又作了进一步的简化，设计出了很简单而且很接近硬件的 B 语言（取 BCPL 的首字母），并且用 B 语言写了初版 UNIX 操作系统（又叫 UNICS）。但 B 语言过于简单，功能有限。



### 1972 年 C 语言（The C Programming Language）诞生

1972 年，美国贝尔实验室的丹尼斯・里奇（D.M.Ritchie ）在 B 语言的基础上最终设计出了一种新的语言，他取了 BCPL 的第二个字母作为这种语言的名字，这就是 **C 语言 **。1973 年初，C 语言的主体完成。汤普森和里奇迫不及待地开始用它完全重写了 **UNIX 操作系统 **。

研制 C 语言的初衷是用它编写 UNIX 系统程序，因此，它实际上是 UNIX 的 “副产品”。它充分结合了汇编语言和高级语言的优点，高效而灵活，又容易移植。

1971 年，同样酷爱 Space Travel 的丹尼斯・里奇（D.M.Ritchie ）为了能早点儿玩上游戏，加入了汤普森的开发项目，合作开发 UNIX。他的主要工作是改造 B 语言，使其更成熟。

1972 年，美国贝尔实验室的丹尼斯・里奇（D.M.Ritchie ）在 B 语言的基础上最终设计出了一种新的语言，他取了 BCPL 的第二个字母作为这种语言的名字，这就是 C 语言。

1973 年初，C 语言的主体完成。汤普森和里奇迫不及待地开始用它完全重写了 UNIX（第一个真正的操作系统）。此时，编程的乐趣使他们已经完全忘记了那个 “Space Travel”，一门心思地投入到了 UNIX 和 C 语言的开发中。



**C 语言既保持 BCPL 语言和 B 语言的优点（精练、接近硬件），又克服了他们的缺点（过于简单，数据无类型等）。**

C 语言是一门**面向过程的、抽象化**的通用程序设计语言，广泛应用于底层开发。C 语言能以简易的方式编译、处理低级存储器。

C 语言是仅产生少量的机器语言以及不需要任何运行环境支持便能运行的高效率程序设计语言。尽管 C 语言提供了许多低级处理的功能，但仍然保持着跨平台的特性，以一个标准规格写出的 C 语言程序可在包括类似嵌入式处理器以及超级计算机等作业平台的许多计算机平台上进行编译。



### 1983 年 C++（The C++ Programming Language/c plus plus ）诞生

  20 世纪 70 年代中期，本贾尼・斯特劳斯特卢普（Bjarne Stroustrup）在剑桥大学计算机中心工作。他使用过 Simula 和 ALGOL，接触过 C。他对 Simula 的类体系感受颇深，对 ALGOL 的结构也很有研究，深知运行效率的意义。既要编程简单、正确可靠，又要运行高效、可移植，是 Bjarne Stroustrup 的初衷。以 C 为背景，以 Simula 思想为基础，正好符合他的设想。
  1979 年，本贾尼・斯特劳斯特卢普（Bjame Sgoustrup）到了 Bell 实验室，开始从事将 C 改良为带类的 C（C with classes）的工作。1983 年该语言被正式命名为 C++。
  C++ 是 C 语言的继承，它既可以进行 C 语言的过程化程序设计，又可以进行以抽象数据类型为特点的基于对象的程序设计，还可以进行以继承和多态为特点的面向对象的程序设计。C++ 擅长面向对象程序设计的同时，还可以进行基于过程的程序设计。

  后来 Sun 公司又对 C++ 进行改写，产生了 Java。而微软公司发现 Java 很流行，就造出了一个类似的语言 ——C#。所以 Java 和 C# 都源自于 C++。

## 2. Java 的诞生

### Oak 的研发

1991 年 Java 的前身 "Oak" 语言开始着手研发。

20 世纪 90 年代（1990 年代），硬件领域出现了单片式计算机系统，这种价格低廉的系统一出现就立即引起了自动控制领域人员的注意，因为使用它可以大幅度提升消费类电子产品（如电视机顶盒、面包烤箱、移动电话等）的智能化程度。美国 Sun 公司（Stanford University Network Microsystems 它其实是斯坦福大学校园网首字母缩写）为了抢占市场先机，在 1991 年成立了一个称为 Green 的项目小组。

项目成员包括帕特里克、**詹姆斯・高斯林**（一个全能的计算机奇才，计算机科学博士，Java 创始人之一，一般公认他为 **"Java 之父"**）、麦克・舍林丹和其他几个工程师一起组成的工作小组在加利福尼亚州门罗帕克市沙丘路的一个小工作室里面研究开发新技术，专攻计算机在家电产品上的嵌入式应用。
![詹姆斯・高斯林个人简介](https://raw.githubusercontent.com/mapleincode/images/master/uPic/watermark,type_ZHJvaWRzYW5zZmFsbGJhY2s,shadow_50,text_Q1NETiBAbHl3U3R1ZGluZw==,size_20,color_FFFFFF,t_70,g_se,x_16-20220811184413294.png)

由于 Sun 公司的人有 Unix 系统的应用背景，而且 C++ 所具有的优势，该项目组的研究人员首先考虑采用 C++ 来编写程序。但对于硬件资源极其匮乏的单片式系统来说，C++ 程序过于复杂和庞大。另外由于消费电子产品所采用的嵌入式处理器芯片的种类繁杂，如何让编写程序跨平台运行也是个难题。

为了解决困难，他们首先着眼于语言的开发，假设了一种结构简单、符合嵌入式应用需要的硬件平台体系结构并为其制定了相应的规范，其中就定义了这种硬件平台的二进制机器码指令系统（即后来成为 “字节码” 的指令系统），以待语言开发成功后，能有半导体芯片生产商开发和生产这种硬件平台。

对于新语言的设计，Sun 公司研发人员并没有开发一种全新的语言，而是根据嵌入式软件的要求，对 C++ 进行了改造，去除了留在 C++ 的一些不太实用及影响安全的成分并结合嵌入式系统的实时性要求，开发了一种称为 Oak（Java 的前身代号 “橡树”，詹姆斯・高斯林这么起名大概是因为非常喜欢自己办公室外的一颗橡树) 的面向对象语言。

### Oak 的搁置和转换

1992 年 "Oak" 语言开发成功后，由于各种原因无法进入市场，被搁置了下来。

由于在开发 Oak 语言时，尚且不存在运行字节码的硬件平台，所以为了在开发时可以对这种语言进行试验研究，他们就在已有的硬件和软件平台基础上，按照自己所指定的规范，用软件建设了一个运行平台，整个系统除了比 C++ 更加简单之外，没有什么大的区别。1992 年的夏天，当 Oak 语言开发成功后（开发这个版本花费了 18 个月），研究者们向硬件生产商演示了 Green 操作系统、Oak 的程序设计语言、类库和其硬件，以说服他们使用 Oak 语言生产硬件芯片，但是，硬件生产商并未对此产生极大的热情。因为他们认为，在所有人对 Oak 语言还一无所知的情况下，就生产硬件产品的风险实在太大了，所以 Oak 的语言也就因为缺乏硬件的支持而无法进入市场，从而被搁置了下来。

1992 年， Green 项目发了第一个产品，称之为 “*7”，这个产品可以提供非常智能的远程控制，遗憾的是， Sun 公司对这个产品并不感兴趣。Green 项目组的人员必须找出其他的方法来将他们的技术推向市场。然而，仍然没有任何一 - 家标准 消费品电此感兴趣。于是，Green 项目组投标了一个设计有线电视盒的项目，它能提供视频点播等多型有线服务，但他们没能拿到这个合同 (有趣的是，得到这个项目的公司的领导恰恰是开始 Netscape 公司的 Jim Clark。Netscape 公司后来对 Java 的成功给予了很大的帮助)。

1993 年，Green 项目（这时换了一个新名字 —— “First Person 公司”）在一这一整年以及 1994 年上半年， 一直苦苦寻求买家购买他们的技术。 然后一个也没有找到 (Patrick Naughton 他项目组的创始人之一，也是完成大多数营销工作的人，声称为了销售这项技术，已累计飞行了 300,000 英里)， 然后在 1994 年，Frist Person 公司解散了。

此时 Internet 的万维网也日渐发展壮大，当时的万维网关键是把浏览器超文本页面转换到屏幕上。在 1994 年大多数都在使用 Mosaic 浏览器（NCSA Mosaic，或简称 Mosaic，是互联网历史上第一个普遍使用和能够显示图片的网页浏览器），这是 1993 年出自伊利诺伊大学超级计算中心的一个非商业化的 Web 浏览器 (Mosaic 的一部分是由 Marc Andreessen 编写的。当时，他作为一名参加半工半读项目的本科生，编写了这个软件，每小时的薪水只有 6.85 美元。他后来成了 Netscape 公司的创始人之一和技术总监， 可谓名利双收)。

在接受 SunWorld 采访的时候，Gosling 说，在 1994 年中期，Java 语言的开发者意识到， 在客户 / 服务器（CS 框架）主流框架中，能够建立一个相当酷的浏览器， 因为浏览器恰好需要的功能：体系结构中立、实时、可靠、安全。1994 年 6、7 月间，在经历了一场历时三天的讨论之后，团队决定再一次改变了努力的目标，这次他们决定将该技术应用于万维网。他们认为随着 Mosaic 浏览器的到来，因特网正在向同样的高度互动的远景演变，而这一远景正是他们在有线电视网中看到的。



### Java 的正式诞生

1995 年 5 月 23 日 ，在 Oak 更名为 Java 后，Java 在 Sun world 会议上正式诞生。

1995 年，互联网的蓬勃发展给了 Oak 机会。业界为了使死板、单调的静态网页能够灵活起来，急需一种软件技术来开发一种程序，这种程序可以通过网络传播并且能够跨平台运行。于是，世界各大 IT 企业维持纷纷投入了大量的人力、物力和财力。这个时候，Sun 公司想起了那个被搁置起来很久的 Oak，并且重新审视了那个用软件编写的试验平台，由于它是按照嵌入式系统硬件平台体系结构进行编写的，所以非常小，特别适用于网络上的传输系统，而 Oak 也是一种精简的语言，程序非常小，适合在网络上传输。

Sun 公司首先推出了可以嵌入网页并且可以随同网页在网络上传输的 Applet ( Applet 是一种将小程序嵌入到网页中进行执行的技术)，并将 Oak 更名为 Java (在申请注册商标时，发现 Oak 是一种已经有的计算机语言的名字，它的商标已经被使用了，在想了一系列名字之后，最终，使用了提议者在喝一杯 Java 咖啡时无意提到的 Java 词语)。

1995 年 5 月 23 日，Sun 公司在 Sun world 会议上正式发布 Java 和 HotJava 浏览器。实际的浏览器是由 Patrick Naughton 和 Jonathan Payne 开发的，并演变为 HotJava 浏路器. HotJava 浏览器采用 Java 编写，为了炫耀 Java 语言的超强能力。这个浏览器可以在网页中执行内嵌的 Java 代码，也就是 Java Applet。

随后 IBM、Apple、DEC、Adobe、HP、Oracle、Netscape 和微软等各大公司都纷纷停止了自己的相关开发项目，竞相购买了 Java 使用许可证，并为自己的产品开发了相应的 Java 平台。  

**在 1995 年 5 月推出的 Java 版本，仅仅只是 Alpha 和 Beta 版，API 极其不稳定**



## 3. Java 的发展

### 早期版本代号

Java 早期各版本代号如下所示：

>  Oracle 官网参考来源：https://www.java.com/releases/

>     Code names were used for early Java releases; the Major releases were named after birds or mammals, while the minor releases were named after insects (given they were bug-fix releases). Hopper was short for grasshopper and ladybird is the British name used for a ladybug. The 1.2 release was called “Playground” by some, but that was never an official or broadly-used code name. The practice of naming releases ended with JDK 7.
>
>     代号用于早期的 Java 版本；主要版本以鸟类或哺乳动物命名，而次要版本主要以昆虫命名 (考虑到它们是 bug 修复版本)。 Hopper 是 grasshopper (蚱蜢) 的缩写，ladybird (瓢虫) 是瓢虫的英国名字。 1.2 版本被一些人称为 “Playground（游乐场）”，但这从来都不是一个官方或广泛使用的代号。 而且 JDK 7（不包括 JDK7）之后不再为版本命名代号了。



| 版本     | 代号                                                       |
| -------- | ---------------------------------------------------------- |
| 7        | Dolphin（海豚）                                            |
| 6        | Mustang（野马）                                            |
| 5.0      | Tiger（老虎）                                              |
| 1.4.2    | Mantis（螳螂）                                             |
| 1.4.1    | Hopper（蚱蜢，grasshopper 的缩写）                         |
| 1.4.0    | Merlin（灰背隼）                                           |
| 1.3.1    | Ladybird（瓢虫）                                           |
| 1.3.0_01 | Firefly（萤火虫）                                          |
| 1.3.0    | Kestrel（美洲红隼）                                        |
| 1.2.2    | Cricket（蟋蟀）                                            |
| 1.2.1    | Gnat（蚋蚊）                                               |
| 1.1.8    | Chelsea（切尔西–城市名，伦敦自治城市，为文艺界人士聚居地） |
| 1.1.7    | Brutus（布鲁图–古罗马政治家和将军）                        |
| 1.1.6    | Abigail（阿比盖尔–女子名）                                 |
| 1.1.5    | Pumpkin（南瓜）                                            |
| 1.1.4    | Sparkler（宝石）                                           |





### JDK 1.0

1996 年 1 月 23 日 Sun 发布 JDK 1.0 版本。



**JDK 1.0 版本代表性技术有：**

1. **初版 Java 虚拟机：Sun Classic VM**
2. **Applet：Java 小应用程序**
3. **AWT：Java 图形设计**



1996 年 1 月，Sun 公司发布了 Java 的第一个开发工具包（JDK 1.0），这是 Java 发展历程中的重要里程碑，标志着 Java 称为一个独立的开发工具。9 月，约 8.3 万个网页应用了 Java 技术来制作。10 月，Sun 公司发布了 Java 平台的第一个即时（JIT）编译器。JIT 编译器和传统编译器不同，传统的编译器是编译一条，运行完成后将其扔掉。而 JIT 会将经常用到的指令保存在内存中，当调用时就不需要重新编译了，效率有了较大提升。



**JDK 1.0 是纯解释运行，使用外挂 JIT，性能比较差，运行速度慢。**





### JDK 1.1

1997 年 2 月 18 日 Sun 发布 JDK 1.1 版本。



JDK 1.1 版本代表技术有：

1. **JDBC**：Java 数据库连接 Java DataBase Connectivity
2. **JavaBeans**： 规范
3. **RMI**：远程调用
4. **JAR** ：文件格式



语言上的新特性：
   1. **Inner Class**：添加内部类支持
   2. **Reflection**：引入反射



1997 年 2 月，JDK1.1 面世，在随后的 3 周时间里，达到了 22 万次的下载量。4 月 2 日，Java One 会议召开，参会者逾一万人，创当时全球同类会议规模之记录。9 月，Java Developer Connection 社区成员超过 10 万。



### JDK 1.2

1998 年 12 月 4 日 Sun 发布 JDK 1.2.0 版本。



JDK 1.2 版本代表技术有：

1. **Swing**
2. **JavaIDL**： 平台对象请求代理体系结构
3. **EJB**
4. **Java Plug-in**：Java 运行插件
5. **内置 JIT（Just In Time）编译器**
6. **字符串常量做内存映射**
7. **对打包的 Java 文件进行数字签名**
8. **控制授权访问系统资源的策略工具**
9. **JDBC 中引入可滚动结果集，BLOB,CLOB，批量更新和用户自定义类型**
10. **在 Applet 中添加声音支持**



语言上的新特性：

1. **引入 Collections 集合类**
2. **strictfp 关键字**（对浮点数计算使用严格的规则）



**该版本中虚拟机第一次内置了 JIT（Just In Time） 即时编译器。**
  

1998 年 12 月 8 日，第二代 Java 平台的企业版 **J2EE** 发布。



---

**摘抄自《深入理解 JVM 虚拟机 ——JVM 高级特性与最佳实践》**



**1996 年 1 月 23 日，JDK 1.0 发布，并提供了 Sun Classic VM 虚拟机 **

从 1996 年初 Sun 发布的 JDK 1.0 中包含 Sun Classic 虚拟机到今天，曾经涌现、湮灭过许多经典，或优秀，或有特色，或有争议的虚拟机实现。

以今天的视角来看，Sun Classic 虚拟机的技术已经相当原始，这款虚拟机的使命也早已终结。但仅凭它 **“世界上第一款商用 Java 虚拟机”** 的头衔，就足够有理由记住它。

Sun Classic 虚拟机只能使用纯解释器方式来执行 Java 代码，如果要使用即时编译器那就必须要进行外挂，但是加入使用外挂了即时编译器的话，即时编译器就会完全接管虚拟机的执行系统，解释器便不能工作了。由于解释器和编译器不能配合工作，这就意味着如果要使用编译执行，编译器就不得不对每一个方法、每一行代码都进行编译，而无论它们执行的频率是否具有编译的价值。基于程序响应时间的压力，这些编译器根本不敢应用编译耗时稍高的优化技术，因此这个阶段的虚拟机虽然用了即时编译器输出本地代码，其执行效率也和传统的 C/C++ 程序有很大差距，“Java 语言很慢” 的印象就是在这个阶段开始在用户心中树立起来的。



**Exact VM**
Sun 的虚拟机团队努力去解决 Classic 虚拟机所面临的各种问题，提升运行效率，在 JDK 1.2 时，曾在 Solaris（Sun 公司研发的一款操作系统）平台上发布过一款名为 **Exact VM 虚拟机 **，它的编译执行系统已经具备现代高性能虚拟机雏形，如热点探测、两级即时编译器、编译器与解释器混合工作模式等。Exact VM 因它使用准确内存管理而得名。准确内存管理是指虚拟机可以知道内存中某个位置的数据具体是什么类型。由于使用了准确内存管理，Exact VM 可以抛弃掉以前 Classic VM 基于句柄（Handle）的对象查找方式。

虽然 Exact VM 的技术相对 Classic VM 来说先进了许多，但是它的命运显得十分英雄气短，在商业应用上只存在了很短暂的时间，在 JDK 1.2 时，就被外部引进的 HotSpot VM 所取代。

在 JDK 1.2 中，曾并存过三个虚拟机，Classic VM，HotSpot VM 和 Exact VM**，** 其中 Exact VM 只在 Solaris 平台出现过。后面两款虚拟机都是内置了 JIT 即时编译器的，而之前版本所带的 Classic VM 只能以外挂的形式使用即时编译器。

Classic VM 的生命周期则相对要长不少，它在 JDK 1.2 之前是 JDK 中唯一的虚拟机，在 JDK 1.2 时，它与 HotSpot VM 并存，但默认是使用 Classic VM（用户可以通过参数切换至 HotSpot VM）。而在 JDK 1.3 时，HotSpot VM 成为默认虚拟机，Classic VM 作为备用选择发布。知道 JDK 1.4 的时候，Classic VM 才完全退出商用虚拟机的历史舞台，与 Exact VM 一起进入了 Sun Labs Research VM 之中。



**1999 年 4 月 27 日 HotSpot 虚拟机发布 **

1999 年 4 月 27 日，HotSpot 虚拟机发布。HotSpot 最初由一家名为 “Longview Technologies” 的小公司开发，由于 HotSpot 的优异表现，这家公司在 1997 年 被 Sun 公司收购。 **HotSpot 虚拟机发布时是作为 JDK 1.2 的附加程序提供的，后来它成为了 JDK 1.3 及之后所有版本的 Sun JDK 的默认虚拟机，同时也是后来 OpenJDK 默认使用的 Java 虚拟机。**

Oracle 收购 Sun 之后，把原来 BEA JRockit 中的优秀特性融合到 HotSpot 之中，到了 2014 年的 JDK 8 时，HotSpot 在这个过程里移除掉永久代，吸收了 JRockit 的 Java Mission Control 监控工具等功能。

```
提示：有关更多 Java 虚拟机相关的内容，参见《深入理解 JVM 虚拟机 ——JVM 高级特性与最佳实践》
1
```



**在 JDK 1.2 版本中 sun 公司把 Java 体系分为 J2SE、J2ME、J2EE 三个方向**



1999 年 6 月，Sun 公司发布了第二代 Java 平台（简称 Java2）的 3 个版本。

第二代 Java 平台的三个方向：

- J2SE（Java 2 Standard Edition，Java2 平台的标准版）
- J2ME（Java 2 Micro Edition，Java2 平台的微型版）
- J2EE（Java 2 Enterprise Edition，Java 2 平台的企业版）



在 Java SE 6（包含）发布之后，Sun 公司把 Java 平台的三个方向名称修改如下：

- Java SE（Java Platform, Standard Edition）：
    - 支持面向桌面级应用（如 Windows 下的应用程序）的 Java 平台，提供了完整的 Java 核心 API。

- Java EE（Java Platform, Enterprise Edition）：
    - 支持使用多层架构的企业应用（如 ERP、MIS、CRM 应用）的 Java 平台，除了提供 Java SE API 外，还对其做了大量有针对性的扩充，并提供了相关部署的支持。

- Java ME（Java Platform, Micro Edition）
    - 支持 Java 程序运行在移动终端（手机、PDA）上的平台，对 Java API 有所精简，并加入了移动终端的针对性支持。但是需要注意的是，主要使用 Java 语言开发程序的 Android 并不属于 Java ME。

- Oracle 后面还有一个 Java Card：支持 Java 小程序（Applets）运行在小内存设备（如智能卡）上的平台。



Java 2 平台的发布，是 Java 发展过程中最重要的一个里程碑，标志着 Java 的应用开始普及。



### JDK 1.3

2000 年 5 月 8 日 Sun 发布 J2SE 1.3.0 版本，代号 Kestrel（美洲红隼）



**JDK 1.3 版本代表性技术有：**

1. **新添加了 Java Sound 类库**
2. **引入 jar 文件索引**
3. **Java Platform Debugger Architecture 用于 Java 调式的平台。**
4. **新的 Timer API**
5. **使用 CORBA IIOP 来实现 RMI 的通信协议**
6. **提供了大量新的 Java 2D API**
7. **JNDI 服务 从 JDK 1.3 开始被作为一项平台级服务提供（以前是一项扩展服务）**



**性能增强：**
	1. **提供了性能更好的数学运算（提供了 java.lang.StrictMath 和 java.lang.Math 类）**



[JDK 1.3 Performance Enhancements（性能增强）详情参考官方解释](https://docs.oracle.com/javase/1.5.0/docs/guide/performance/speed.html)



2000 年 5 月，JDK 1.3 、JDK 1.4 和 J2SE 1.3 相继发布，几周后其获得了 Apple 公司 Mac OS X 的工业标准支持。2001 年 9 月 24 日，J2EE1.3 发布。JDK 1.3 和后面的 JDK 1.4 在官网看到也有称为：[J2SDK 1.3](https://docs.oracle.com/javase/1.5.0/docs/guide/performance/speed.html)、[J2SDK 1.4](https://docs.oracle.com/javase/1.5.0/docs/guide/performance/speed.html)



### JDK 1.4

2002 年 2 月 13 日 Sun 发布 J2SE 1.4.0 版本，代号 Merlin（灰背隼）。



**JDK 1.4 版本代表技术有：**

1. **添加 XML 处理**
2. **添加 Java 打印服务（Java Print Service API）**
3. **引入 Logging API**
4. **引入 Java Web Start**
5. **引入 JDBC 3.0 API**
6. **引入 Preferences API**	
7. **引入链式异常处理**
8. **支持 IPV6**
9. **支持正则表达式**
10. **引入 Image I/O API**
11. **NIO（New I/O），非阻塞的 IO，优化 Java 的 IO 读取**
12. **XSLT 转换器（可扩展样式表转换语言 Extensible Stylesheet Language Transformations，是一种样式转换标记语言，可以将 XML 数据档转换为另外的 XML 或其它格式，如 HTML 网页，纯文字）**



**语言新特性（New Language Features）：**

1. **Assertion Facility—— 断言工具，断言是程序员认为有关计算机程序状态的布尔表达式。**

   - **例如，在对 list 集合进行排序后，程序员可能会断言该 list 集合是按升序排列的。**

   - **在运行时评估断言以确认其有效性是提高代码质量的最强大工具之一，因为它可以快速揭示程序员对程序行为的误解。**

[JDK 1.4 Performance Enhancements（性能增强）详情参考官方解释](https://docs.oracle.com/javase/1.5.0/docs/guide/performance/speed.html)

[JDK 1.4 New Language Features（语言新特性）详情参考官方解释](https://docs.oracle.com/javase/1.5.0/docs/guide/language/index.html)



2002 年 2 月 26 日，J2SE 1.4 发布。自此 Java 的计算能力有了大幅提升，与 J2SE 1.3 相比，其多了近 62% 的类和接口。在这些新特性中，还提供了广泛的 XML 支持，安全套接字（Socket）支持（通过 SSL 与 TLS 协议）、全新的 I/O API、正则表达式、日志与断言。

2002 年前后还发生一件与 Java 没有直接关系，但事实上对 Java 的发展进程影响很大的事件，就是微软的 .NET Framework 发布。这个无论是技术实现还是目标用户上都与 Java 有很多相近之处的技术平台给 Java 带来了很多讨论、比较与竞争，.NET 平台 和 Java 平台之间声势浩大的孰优孰劣的论战到今天为止都仍然没有完全平息。

### Java SE 5.0

2004 年 9 月 30 日 Sun 发布 Java SE 5.0 版本，代号 Tiger（老虎）



**JDK 5.0 版本语言新特性有：**

1.  **Generics： 泛型**

2. **Enhanced for Loop：增强 for 循环**
3. **Autoboxing/Unboxing：自动装箱 / 拆箱**
4. **Typesafe Enums：类型安全的枚举**
5. **Varargs：可变长参数**
6. **Static Import：静态引入**
7. **Annotations：注解**



**在虚拟机和 API 层面上：**

- **这个版本改进了 Java 的内存模型**
- **提供了 java.util.concurrent 并发包等**



[JDK 5.0 Performance Enhancements（性能增强）详情参考官方解释](https://docs.oracle.com/javase/1.5.0/docs/guide/performance/speed.html)

[JDK 5.0 New Language Features（语言新特性）详情参考官方解释](https://docs.oracle.com/javase/1.5.0/docs/guide/language/index.html)



2004 年 9 月 30 日，J2SE 1.5 发布，成为 Java 语言发展史上的又一里程碑。为了表示该版本的重要性，J2SE 1.5 更名为 Java SE 5.0（内部版本号 1.5.0），代号为 “Tiger”，Tiger 包含了从 1996 年发布 1.0 版本以来的最重大的更新，其中包括泛型支持、基本类型的自动装箱、改进的循环、枚举类型、格式化 I/O 及可变参数。



> 摘抄自《深入理解 Java 虚拟机 ——JVM 高级特性与最佳实践 第三版》
> 其实 Sun 公司自 JDK 5 时代起，就曾以 JRL（Java Research License）的形式公开过 Java 的源码，主要是开放给研究人员阅读使用，这种 JRL 许可证的开放源代码一直持续到 JDK 6 Update 23 才因 OpenJDK 项目日渐成熟而终止。



### Java SE 6

2006 年 12 月 12 日 Sun 发布 Java SE 6 版本（这个版本开始没有后缀.0），代号 Mustang（野马）



**JDK 6 版本主要更新内容有：**
	1. **引入了一个支持脚本引擎的新框架（基于 Mozilla Rhino 的 JavaScript 脚本引擎）**
	2. **UI 的增强**
	3. **对 WebService 支持的增强（JAX-WS2.0 和 JAXB2.0）**
	4. **引入 JDBC4.0API**
	5. **引入 Java Compiler API**
	6. **通用的 Annotations 注解支持**



**这个版本对 Java 虚拟机内部做了大量改进：**

1. **锁与同步**

2. **垃圾收集**

3. **类加载**

   **等方面的实现都有相当多的改动。**



[JDK 6 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/features.html)



2006 年 12 月 12 日 Sun 发布 Java SE 6 版本（这个版本开始没有后缀 .0 ，之前的 JDK 5.0 中的 .0 也被去除）。此时的各种版本已经更名，已取消其中的数字 2，如 J2EE 更名为 **JavaEE（Java Platform, Enterprise Edition）**，J2SE 更名为 **JavaSE（Java Platform, Standard Edition）**，J2ME 更名为 **JavaME（Java Platform, Micro Edition）**。



**摘抄自《Java 核心技术 卷 1》**

*Java 开发工具包（Java Development Kit ，简称 JDK），这个工具包在版本 1.2~ 版本 1.4 被称为 Java SDK (Java 软件开发包，Software Development Kit)。*

  *Java 2 这种提法始于 1998 年，当时 Sun 公司的销售人员感觉增加小数点后面的数值改变版本号并没有反映出 JDK1.2 的重大改进。*

  *由于在发布之后才意识到这个问题，所以决定开发工具包的版本号任然沿用 1.2，接下来的版本是 1.3、1.4 和 5.0。但是，Java 平台被重新命名为 Java 2。*
  *因此，就有了 Java 2 Standard Edition Software Development Kit (Java 2 标准版软件开发包) 的 5.0 版，即 J2SE SDK 5.0*
  *幸运的是，2006 年版本号得到简化。Java 标准版的下一个版本取名为 Java SE 6，后来又有了 Java SE 7 和 Java SE 8。不过，内部版本号分别是 1.6.0、1.7.0 和 1.8.0*

  *当 Oracle 为解决一些紧急问题作出某些微小的版本改变时，将其称为更新。*
  *例如：Java SE 8u31 是 Java SE 8 的第 31 次更新，它的内部版本号是 1.8.0_31。另外，并不是所有更新都公开发布，所以如果 “更新 31” 之后没有 "更新 32"，也不必惊慌*



**官方参考来源：https://www.java.com/releases/**

*When first released Java was named Java Development Kit, abbreviated JDK. With the release of version 2 there was a rebranding to Java 2 - full name: Java 2 Software Development Kit, abbreviated to Java 2 SDK or J2SDK. This was reverted back to JDK with JDK 6 and has stayed that way since.*

*第一次发布时，Java 被命名为 Java Development Kit，缩写为 JDK。 随着版本 2 的发布，重新命名为 Java 2： 全名：Java 2 软件开发工具包，缩写为 Java 2SDK 或 J2SDK。 在 JDK 6 中又恢复了这种情况，并一直保持至今。*





**Java SE 各版本类型名词解释如下:**

| 类型                                           | 描述                                                         |
| ---------------------------------------------- | ------------------------------------------------------------ |
| Major/Feature Release                          | 表示每个大版本的更新。可以更改 Java 规范的版本：可以添加、弃用或删除 api。 随着 JDK 10 的发布，发布模型被更新为日期驱动 (每六个月一次) 而不是内容驱动。 主要版本被重命名为特性版本以反映这种变化。 |
| Minor Release                                  | 带有大量修复或引入新功能、端口或操作系统升级的版本。         |
| Update Release                                 | 专注于 bug 修复和平台 / 第三方更新，直到 CPU 版本的引入也包含漏洞修复。 |
| Patch Release                                  | 包含关键的 bug 修复，但没有附加的安全漏洞修复。                |
| CPU（Critical Patch Updates）/Security Release | Java SE 重要补丁更新（CPU，奇数版本，如 8u201）包含对安全漏洞和关键错误修复的修复。 Oracle 强烈建议所有 Java SE 用户在可用时升级到最新的 CPU 版本。Java SE CPU 版本是奇数版本（即 7u71，7u65 - 请参阅 [此处](https://link.ld246.com/forward?goto=https%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjava-update-release-numbers-change-1836624.html) 有关 Java SE 版本编号方案的更多 [信息](https://link.ld246.com/forward?goto=https%3A%2F%2Fwww.oracle.com%2Ftechnetwork%2Fjava%2Fjava-update-release-numbers-change-1836624.html)）。 |
| PSU（Patch Set Update） Release                | Java SE 补丁集更新（PSU，偶数版本，如 8u202）包含相应 CPU 中的所有修复程序，以及其他非关键修复程序。 只有在您受到该版本中修复的其他错误之一的影响时，才应使用 Java PSU 版本。补丁集更新 (Patch Set Update, PSU) 版本与 CPU 同一天发布，包含额外的功能补丁和漏洞补丁，当有对应的 PSU 时，CPU 就不会包含一般的 bug 补丁。 参考网址：https://ld246.com/article/1547793619176 但是从 JDK 11 和 JDK 17 的版本号来看似乎没有奇数和偶数这样的版本了。 |
| ER/SA                                          | 漏洞 (安全警报 - SA) 或非漏洞 (紧急发布 - ER) 修复的计划外发布。   |
| Performance Releases                           | 发行版 (以 p 结尾) 关注性能改进，通常包含 HotSpot 的更新版本，并附带新的性能基准测试结果。 |
| GA Release                                     | General Availability Release，可供商用的版本                 |
| EA Release                                     | Early-Access Release，最近通过发布的使用版本                 |
| BPR                                            | Bundled Patch Release，补丁版本                              |

官方参考来源：

1. https://www.java.com/releases/
2. [Oracle 官网重要补丁更新、安全警报和公告](https://www.oracle.com/security-alerts/)



### Sun 公司开源 Java



2006 年 11 月 13 日，Sun 公司宣布，它将在 GNU 通用公共许可第二版，类路径例外（即 GNU General Public License, version 2, with the Classpath Exception）下，将 Java 技术作为免费开源软件对外发布，并陆续开放源代码。



2006 年 11 月 13 日，Java 技术的发明者 Sun 公司宣布，它将在 GNU 通用公共许可第二版，类路径例外（即 [GNU General Public License, version 2, with the Classpath Exception](https://openjdk.org/legal/gplv2+ce.html)）下，将 Java 技术作为免费开源软件对外发布。



Sun 公司正式发布的有 Java SE（Java Platform, Standard Edition） 的第一批源代码，以及 Java ME（Java Platform, Micro Edition） 的可执行源代码。



> 疑问：**Java 彻底开源了吗？** 参考来源：[开源中国](https://www.oschina.net/news/79031/is-java-opensource)
>   2006 年 11 月 13 日，Sun Microsystems 正式开源 Java。事实上 Java 的源代码在发布第一天就可以访问，这一策略帮助 Java 在早年推动了平台的建立。
>   Java 之父 James Gosling 称，他们公开源代码的意图是让社区帮助进行安全分析、bug 报告、增强性能、了解偏僻个案等。Java 最早使用的许可证允许内部使用源代码但不能分发。
>   IBM 当时呼吁将 Java 捐给 Apache 软件基金会，使用 Apache 许可证分发，但 Sun 最终决定在 GPL 许可证下开源 Java。GPL 许可证要求衍生版本需要公开分发。Gosling 称这能让 Java 更好的适应开源社区。Sun 在 2010 年被甲骨文收购，Java 也落到了甲骨文手中，由甲骨文主导 Java 的演化。
>   然而 Java 开源十年之后仍然引起争议，因为 Java 的开源被认为是打了折扣。批评者称，Sun 只开源了 JDK，Java SE 和 Java EE TCKs 仍然是闭源的。



###  Sun 公司成立了 OpenJDK 开源社区组织

2006 年 11 月 Sun 公司成立了 OpenJDK 开源社区组织（不只有 OpenJDK 中的 jdk 项目，还有和 Java 相关的其它开源项目），对源码进行独立管理



2006 年 11 月 Sun 公司 成立了 **[OpenJDK 开源组织](https://openjdk.org/)** 对这些源码进行独立管理。只有极少量的产权代码除外（Encumbered Code, 这部分代码大多是 Sun 公司本身也无权进行开源处理的，后来被替换为功能相同的开源代码 ），从此 OpenJDK 诞生。



2007 年 3 月起，全世界所有的开发人员均可对 Java 源代码进行修改。

**OpenJDK 社区 ** 是协作实现 **JavaSE** 和 **相关项目** 的开源实现的地方。

**OpenJDK（Open Java Development Kit）是 Java SE（Java Platform，Standard Edition）的开源实现。**

参见：https://openjdk.org/guide/#introduction

![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/d0729eb72fff48f38d4cb2154f17a992.png)



注意：

1. 这里说的 【OpenJDK】 一般是指 【OpenJDK 开源社区】 中的 【jdk 项目】，OpenJDK 开源社区 还有很多其它跟 Java 相关的开源项目	
2. 这里说的 【许可证】、【许可协议】、【协议】、【许可】 表示都是一个意思



  OpenJDK 的许可协议主要包含了 **GPL v2**（GNU General Public License, version 2）以及 **GPL v2 + ClassPath Exception** (GNU General Public License, version 2,with the Classpath Exception)， 还有一些是其他协议以及没有协议的文件。

> **GPL v2 开源协议用一句话解释就是：我给你源码，你给我它的修改。**

> 参考来源：[stackoverflow：OpenJDK 是在什么开源许可证下发布的？](https://stackoverflow.com/questions/16167562/jdk-source-code-license-inconsistency)
> What open-source license is OpenJDK published under?
>   GPL v2 for almost all of the virtual machine, and GPL v2 + the Classpath Exception for the class libraries and those parts of the virtual machine that expose public APIs.
>   
> OpenJDK 是在什么开源许可证下发布的？
>   GPL v2 适用于几乎所有的虚拟机，GPL v2 + ClassPath Exception 适用于类库和虚拟机中暴露公共 API 的部分。

> 参考来源：[为什么 ClassPath Exception 如此重要？](https://www.azul.com/blog/why-the-classpath-exception-is-so-important/)
>   Well, OpenJDK, the open-source implementation of the Java platform, is also licensed under the GPLv2. At least, most of it is; several components have been contributed to the project that uses other licenses like the BSD, Apache or MIT ones. Without some modification, the copyleft nature of the GPL would also extend to any code using the JVM, i.e. any Java application would need to be licensed under the GPLv2!
>   Clearly, that would not have been good for the Java ecosystem, and we would have seen significantly less adoption than we have since the creation of the OpenJDK project in 2007.
>   Some people who look at this assume that all GPLv2 licensed code in OpenJDK includes the CPE, but this is not the case. If you search the hotspot source code directory (I took the one from jdk16u), of the 3291 files, only five include the Classpath exception. Conversely, looking at the java.base source directory, only 54 of the 3351 files don’t have the Classpath exception. This is because the Classpath exception is only required when application code touches JDK code directly.
>   
>   OpenJDK 最早大多数都是在 GPL v2 协议下开源的，OpenJDK 这个项目中已经有几个组件使用了其它的的许可，例如 BSD、Apache 或 MIT 的许可。 ** 如果不做一些修改，GPL 的 copyleft 特性也会扩展到任何使用 JVM 的代码，也就是说，任何 Java 应用程序都需要在 GPL v2 许可下开源！**
>   很明显，这对 Java 生态系统来说不是好事，如果不使用 **ClassPath Exception**, 我们将会看到自 2007 年 OpenJDK 项目创建以来，采用 Java 的人会明显减少。
>   有些人认为 OpenJDK 中所有 GPLv2 许可的代码都包含 CPE（ClassPath Exception，类路径例外），但事实并非如此。 搜索 HotSpot 源代码目录 (我从 jdk16u 获取了该目录)，在 3291 个文件中，只有 5 个文件包含 ClassPath Exception。 相反，看看 java。 在基本源目录下，3351 个文件中只有 54 个没有 ClassPath Exception。 这是因为只有当应用程序代码直接接触 JDK 代码时才需要 ClassPath Exception。

下面是 [ClassPath Exception](https://www.gnu.org/software/classpath/home.html) 原文：

```
"CLASSPATH" EXCEPTION TO THE GPL

Certain source files distributed by Oracle America and/or its affiliates are
subject to the following clarification and special exception to the GPL, but
only where Oracle has expressly included in the particular source file's header
the words "Oracle designates this particular file as subject to the"Classpath"
exception as provided by Oracle in the LICENSE file that accompanied this code."

    Linking this library statically or dynamically with other modules is making
    a combined work based on this library.  Thus, the terms and conditions of
    the GNU General Public License cover the whole combination.

    As a special exception, the copyright holders of this library give you
    permission to link this library with independent modules to produce an
    executable, regardless of the license terms of these independent modules,
    and to copy and distribute the resulting executable under terms of your
    choice, provided that you also meet, for each linked independent module,
    the terms and conditions of the license of that module.  An independent
    module is a module which is not derived from or based on this library.  If
    you modify this library, you may extend this exception to your version of
    the library, but you are not obligated to do so.  If you do not wish to do
    so, delete this exception statement from your version.

翻译总结如下：
1. OpenJDK 项目中只有在源代码文件中包含（一般在源代码开头）【 Oracle designates this particular file as subject to the “Classpath” exception as provided by Oracle in the LICENSE file that accompanied this code.】这句话，这个源代码文件才是 GPL v2 + ClassPath Exception 协议的。
2. 将自己写的独立模块（不是从 Java 类库派生的或者基于此类库修改后的）的代码动态或者静态的链接到含有 ClassPath Exception 代码，不受 GPL 影响，不用开放源代码（例如，import 一个 Java 类库包）。而且可以使用自己许可协议进行商业收费。
3. 如果有必要，你也可以将 ClassPath Exception 扩展包含到自己写的类库中。
```

> 注意：开源不等于免费，有些厂商利用开源软件，提供维护支持，漏洞补丁更新等商业服务来赚钱



这么做的好处就是，基于 OpenJDK 开发的 应用程序不用受到 GPL 的影响，且不用开源自己写的程序代码，还可以进行商业收费。只有对于 OpenJDK 项目 本身进行源代码二次开发，修改了源代码的 ，在网上发行的话，才需要开源，或者把源代码修改的部分提供给 OpenJDK 社区。



更多关于 GNU ClassPath Exception 的问题，参见：
1、https://www.mend.io/resources/blog/top-9-gpl-with-the-classpath-exception-questions-answered/
2、https://softwareengineering.stackexchange.com/questions/119436/what-does-gpl-with-classpath-exception-mean-in-practice
3、https://qastack.cn/software/119436/what-does-gpl-with-classpath-exception-mean-in-practice

---

注意：

>  Herein lies the question that all JDK users should consider,
>  “How sure are you that all the files that need the CPE have the CPE?” 
>  If even one file is missing the CPE that needs it, it’s the same as no CPE at all.

> 值得注意的是，直接使用 OpenJDK 不能保证你所有 链接的 Java 类库 都包含 ClassPath Exception，即使你使用 OpenJDK 开发的程序中，只有一个链接的类库文件没有 ClassPath Exception，也等于你的项目都没有 CPE ，为此可能会带来 商业纠纷，特别是 Oracle 要找你扯皮。所以在使用 OpenJDK 开发商业软件时要注意链接的类库是否包含 CPE。



**OpenJDK 常用的核心类库中，源代码开头一般都包含了 ClassPath Exception**。
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/4ae053b252124c54875e6b7b4aa126ac.png)



**但是 hotspot 虚拟机中源代码 GC.java 文件并没有包含 ClassPath Exception ，值得注意**
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/7018c218ae0746839ff26fe65edc1a93.png)



### Oracle 收购 Sun

2009 年，甲骨文（Oracle）公司宣布收购 Sun（Stanford University Network Microsystems）公司。

2010 年，Java 编程语言的共同创始人之一詹姆斯・高斯林从 Oracle 公司辞职。

由于此前 Oracle 已经收购了另外一家大型的中间件企业 BEA 公司，当完成对 Sun 公司收购之后，Oracle 分别从 BEA 和 Sun 手中取得了 **世界三大商用虚拟机** 的其中两个：**JRockit** 和 **HotSpot**。（另外一个是 IBM 公司的 **IBM J9 虚拟机，它的职责分离与模块化做得比 HotSpot 更好 **）

当时 Oracle 宣布要在未来一段时间内，把这两个优秀的 Java 虚拟机合二为一。两者合并的结果差强人意，JRockit 的 **JMC**（Java Mission Control，能够监视和管理 Java 应用程序的监控工具）、**JFR**（Java Flight Recorder，OpenJDK 从 11 版本开始支持。它是一个低开销的数据收集框架，可用于在生产环境中分析 Java 应用和 JVM 运行状况及性能问题。）**被移植到了 HotSpot**，HotSpot **用本地内存代替永久代实现方法区**，**支持本地内存使用情况追踪（NMT）等功能也是从 JRockit 借鉴过来的**。



**Oracle 收购 Sun 公司之后，OpenJDK 开源社区主要由 Oracle 领导，Sun JDK 也变成了 Oracle JDK，自然 Oracle JDK 就是 Java SE 的闭源实现。**



### Java SE 7 LTS

2011 年 7 月 11 日，Oracle 发布 Java SE 7 LTS 版本，代号 Dolphin（海豚）



**JDK 7 版本语言新特性有：**

1. **Binary Literals：二进制字面值（0b 或者 0B 开头表示二进制数字 ，0 开头的数字表示八进制，0X 或者 0x 开头的数字表示十六进制）**
2. **Strings in switch Statements：switch 语句表达式可以用 String 字符串**	
3. **The try-with-resources Statement：可以声明一个或者多个资源**
4. **Catching Multiple Exception Types and Rethrowing Exceptions with Improved Type Checking：同时捕获多个异常类型并重新抛出异常**
5. **Underscores in Numeric Literals：数组文字下划线**
6. **Type Inference for Generic Instance Creation**
7. **Improved Compiler Warnings and Errors When Using Non-Reifiable Formal Parameters with Varargs Methods**



**Java Virtual Machine (JVM) 虚拟机相关：**

1. **Java Virtual Machine Support for Non-Java Languages**
2. **Garbage-First Collector 提供新的 G1 收集器（G1 在发布时仍处于 实验性状态 ，直到 2012 年 4 月的 Update 4 中才正式商用）**
3. **Java HotSpot Virtual Machine Performance Enhancements**





[JDK 7 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/jdk7-relnotes.html)

2011 年，甲骨文公司举行了全球新的活动，以庆祝 Java SE 7 正式发布。

Java SE 7 LTS（Long-Term-Support）是长期支持版本，一直维护更新到 **2022 年 7 月 19 日**。



###  Java SE 8 LTS

2014 年 3 月 18 日，Oracle 发布 Java SE 8 LTS 版本。



JDK 8 语言新特性有：

1. Lambda Expressions——Lambda 表达式
2. Method references—— 方法引用
3. Default methods enable new functionality to be added to the interfaces—— 接口可以实现默认方法
4. Repeating Annotations—— 重复注解
5. Type Annotations—— 类型注解
6. Improved type inference—— 增强的类型推断
7. Method parameter reflection—— 方法参数反射
8.  内置 Nashorn JavaScript 引擎的支持
9.  新的时间、日期 API
10.  彻底移除 HotSpot 的永久代

[JDK 8 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/8-whats-new.html)

2014 年，甲骨文公司发布了 Java SE 8 正式版，oracle 原计划 2013 年发布，由于安全性问题两次跳票，是自 Java SE 5 以来最具革命性的版本。  

**后来 2018 年微软收购了 GitHub，2019 年 7 月 9 日 IBM 也收购了 红帽公司。**

红帽现在又接管了 OpenJDK 8、OpenJDK 11 更新项目（之前接管了 OpenJDK 6、7 的更新项目），负责领导 OpenJDK 8 和 11 的长期更新。红帽每年通过 rpm 和 zip 文件为 OpenJDK 8 和 11 发行版提供季度 JRE 和 JDK 更新。

参考网址：

1. [红帽 与 OpenJDK 之间的历史关系](https://www.redhat.com/en/blog/history-and-future-openjdk)
2. [红帽对 OpenJDK 支持的生命周期](https://access.redhat.com/articles/1299013)

  

Java SE 8 LTS（Long-Term-Support）也是长期支持版本，维护更新到 2030 年 12 月。从 JDK 8 开始，Orcale 启用 JEP 来定义和管理纳入新版 JDK 发布范围的功能特性。



###Java SE 9 

2017 年 9 月 21 日，Oracle 发布 Java SE 9 版本

**JDK 9 版本主要更新内容有：**

1. **Java Platform Module System—— 模块化系统**
2. **The Java Shell——JShell 工具**

[JDK 9 Features and Enhancements（特性和增强功能）详情参考官方解释](https://docs.oracle.com/javase/9/whatsnew/toc.htm#JSNEW-GUID-C23AFD78-C777-460B-8ACE-58BE5EA681F6)<——



2017 年 9 月 21 日，Oralce 公司发布了 Java SE 9。这个版本算是比较大的调整，加入了很早之前就规划的模块功能。并且对整个 JDK 基于模块进行了重新编写。

原本 JDK 9 是计划在 2016 年发布的，但在 2016 年伊始，Oracle 就宣布 JDK 9 肯定要延期到 2017 年，后来又经过 2 次短时间的跳票，最终到 2017 年 8 月 21 日才艰难面世。

后 2 次跳票的原因，是以 IBM 和 RedHat 为首的十三家企业在 JCP 执行委员会上联手否决了 Oracle 提出的 Jigsaw 作为 Java 模块化规范进入 JDK 9 发布范围的提案。凭良心说，Java 确实有模块化的刚需，不论是 JDK 自身（例如拆分出 Java SE Embedded 这样规模较小的产品）亦或是 Java 应用都需要用到模块化。

这方面 IBM 本身就是各大 Java 发行厂商中做的最好的，它不仅让自家的 JDK 实现了高度模块化，还带头成立了 **OSGi 联盟 ** ，制订了 Java 框架层面模块化的事实标准，所以他当然会想把 OSGi 推倒 Java 规范里去整个 “名分”，而不是被 Jigsaw 革掉性命。

可是 Oracle 对此没有丝毫退让，不惜向 JCP 发出 [公开信](https://www.infoq.cn/article/2017/05/jigsaw-open-letter)，直言如果提案最后无法通过，那 Oracle 将摈弃 JSR 专家组，独立发展带 Jigsaw 的 Java 版本，Java 顿时面临如 Python 2 与 Python 3 那般分裂的危机。最终经过多轮投票斗争与妥协，Java 没有分裂，JDK 9 总算是带着 Jigsaw（模块化） 最终发布了。

JDK 9 发布之后，Oracle 随即宣布 Java 将会以持续交付的形式和更加敏捷的研发节奏向前推进，以后 JDK 将会在每年的 3 月 和 9 月 各发布一个大版本，目的就是为避免众多功能特性被集中捆绑在一个 JDK 版本上而引发交付风险。这次改革确实从根源上解决了跳票问题，但是也为 Java 的用户和发行商带来了颇大的压力，会感慨，还没用就过时了。Oracle 决定每 6 个 JDK 大版本，才被划出一个长期支持版（LTS）。

2017 年 8 月，Oracle 决定将 Java EE 移交给开源组织，最后 Eclipse 基金会接手，此时是 Java EE 8 版本。由于 Oracle 不允许开源组织用 Java 的名号，于是 Eclipse 选出了 “Jakarta EE” 和 "Enterprise Profile" 两个后续名字，最终以 64.4% 的票数获胜。也就是说 Java EE 已经正式更名为 [Jakarta EE](https://jakarta.ee/)（雅加达）。



### Java SE 10

2018 年 3 月 20 日，Oracle 发布 Java SE 10 版本。

[JDK 10 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/10-relnote-issues.html)

从 2018 年开始，每 6 个月就会发布一个 Java 版本，以更快地引入新特性。这里又分为 LTS 版和最新版。2018 年 3 月 20 日，Java SE 10 发布。

2018 年 3 月 27 日，Android 的 Java 侵权案有了最终判决，法庭裁定 Google 赔偿 Oracle 合计 88 亿美元，要知道 2009 年 Oracle 收购 Sun 也就只花了 74 亿（如果不考虑通货膨胀的话）。
Oracle 公司是典型的只谈利益不讲情怀的公司，InfoWorld 披露的一封 [Oracle 高管邮件](https://www.infoworld.com/article/2987529/insider-oracle-lost-interest-in-java.html) 表明，Java 体系中被认为无法盈利也没有太多战略前景的部分会逐渐被 “**按计划报废**”。

**2018 年 3 月，Oracle 正式宣布将 Java EE 移交给开源组织 Eclipse 基金会，此时是 Java EE 8 版本。** 由于 Oracle 不允许开源组织用 Java 的名号，于是 Eclipse 选出了 “Jakarta EE” 和 "Enterprise Profile" 两个后续名字，最终以 64.4% 的票数获胜。也就是说 Java EE 已经正式更名为 [Jakarta EE](https://jakarta.ee/)（雅加达）。

HotSpot 虚拟机中含有两个即时编译器，分别是**编译耗时短，但输出代码优化程度较低的客户端编译器（C1）以及编译耗时长但输出代码优化质量也更高的服务端编译器（C2）**，通常它们会在分层编译机制下与解释器互相配合来共同构成 HotSpot 虚拟机的执行子系统。

**从 JDK 10 起，HotSpot 中又加入了一个全新的即时编译器：Graal 编译器**，看名字就跟上面提到的 Graal VM 有关。**Graal 编译器 是以 C2 编译器替代者的身份登场的。**





### Java SE 11 LTS

2018 年 9 月 25 日，Oracle 发布 Java SE 11 LTS 版本

[JDK 11 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/11-relnote-issues.html)<——

Java SE 11 LTS（Long-Term-Support）也是长期支持版本，一直维护更新到 2026 年 9 月 30 日。

自 2017 年 9 月 JDK 9 发布开始，Oracle 在免费的开源许可证（GPL v2 + CPE)（类似于 Linux）下提供 OpenJDK 二进制构建版本。Oracle 提供的 OpenJDK 版本的可用性和社区支持在 jdk.java.net 上单独列出。



**Oracle 为 OpenJDK 社区贡献了以下功能：**

1. [Java Flight Recorder](http://openjdk.java.net/jeps/328)
2. [Java Mission Control](http://openjdk.java.net/projects/jmc/)
3. [Application Class-Data Sharing](http://openjdk.java.net/jeps/310)
4. [ZGC](http://openjdk.java.net/jeps/333)\

因此，**从 Java 11 开始，Oracle JDK 构建 和 Oracle OpenJDK 构建 将基本相同**。
但有一些外观和包装差异，进一步表明 **Oracle JDK** 和 **OpenJDK** 功能技术几乎相同
确实仍然存在少量差异，有些是有意的和装饰性的，有些只是因为需要更多时间与 OpenJDK 贡献者讨论。

具体差异细节参见：https://blogs.oracle.com/java/post/oracle-jdk-releases-for-java-11-and-later

Oracle 基于 OpenJDK 构建的 Oracle OpenJDK，更新周期只有 6 个月，在这 6 个月更新期间，Oracle OpenJDK 和 Oracle JDK 几乎没什么区别。6 个月之后，原来版本的 Oracle OpenJDK 不再维护更新，Oracle 会重新构建新版本的 Oracle OpenJDK。

Oracle OpenJDK 可以理解为 Oracle JDK 的试用版或者尝鲜版。



###  Java SE 12

2019 年 3 月 19 日，Oracle 发布 Java SE 12 版本

[JDK 12 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/12-relnote-issues.html#NewFeature)<——

JDK12 中，最引人注目的特性无疑是由 **RedHat 领导开发的 Shenandoah 垃圾收集器**（Oracle JDK 并没有采用）。其目标与 Oracle 在 JDK 11 中发布的 ZGC 几乎完全一致，两者天生就存在竞争。Oracle 马上用实际行动抵制了这个新收集器。

**在 JDK 11 发布时，Oracle 才说应尽可能保证 OracleJDK 和 OpenJDK 的兼容一致，转眼就在 OracleJDK 12 里把 Shenandoah 的代码通过条件编译强行剔除掉，使其成为历史上第一个进入了 OpenJDK 发布清单，但在 OracleJDK 中无法使用的功能。**

###  Java SE 13

2019 年 9 月 19 日，Oracle 发布 Java SE 13 版本

[JDK 13 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/13-relnote-issues.html#NewFeature)



### Java SE 14

2020 年 3 月 17 日，Oracle 发布 Java SE14 版本

#### [JDK 14 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/14-relnote-issues.html#NewFeature)



### Java SE 15

2020 年 9 月 15 日，Oracle 发布 Java SE 15 版本

[JDK 15 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/15-relnote-issues.html#NewFeature)<——



### Java SE 16

2021 年 3 月 16 日，Oracle 发布 Java SE 16 版本

[JDK 16 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/16-relnotes.html#NewFeature)<——



### Java SE 17 LTS

2021 年 9 月 14 日，Oracle 发布 Java SE 17 LTS 版本

[JDK 17 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/17-relnote-issues.html#NewFeature)

Java SE 17 是继 Java 11 之后又一个长期支持版本，并且维护更新到 2029 年 9 月 30 日。

（[有关 JDK 17 免费商用的文章报导](https://www.oracle.com/news/announcement/oracle-releases-java-17-2021-09-14/)）



### Java SE 18

2022 年 3 月 22 日，Oracle 发布 Java SE 18 版本

[JDK 18 Features and Enhancements（特性和增强功能）详情参考官方解释](https://www.oracle.com/java/technologies/javase/18-relnote-issues.html)



## 4. OpenJDK



### OpenJDK 的发展

2009 年，甲骨文（Oracle）公司宣布收购 Sun（Stanford University Network Microsystems）公司。

2010 年，Java 编程语言的共同创始人之一詹姆斯・高斯林从 Oracle 公司辞职。

由于此前 Oracle 已经收购了另外一家大型的中间件企业 BEA 公司，当完成对 Sun 公司收购之后，Oracle 分别从 BEA 和 Sun 手中取得了 **世界三大商用虚拟机** 的其中两个：**JRockit** 和 **HotSpot**。（另外一个是 IBM 公司的 **IBM J9 虚拟机，它的职责分离与模块化做得比 HotSpot 更好 **）

当时 Oracle 宣布要在未来一段时间内，把这两个优秀的 Java 虚拟机合二为一。两者合并的结果差强人意，JRockit 的 **JMC**（Java Mission Control，能够监视和管理 Java 应用程序的监控工具）、**JFR**（Java Flight Recorder，OpenJDK 从 11 版本开始支持。它是一个低开销的数据收集框架，可用于在生产环境中分析 Java 应用和 JVM 运行状况及性能问题。）**被移植到了 HotSpot**，HotSpot **用本地内存代替永久代实现方法区**，**支持本地内存使用情况追踪（NMT）等功能也是从 JRockit 借鉴过来的**。



**Oracle 收购 Sun 公司之后，OpenJDK 开源社区主要由 Oracle 领导，Sun JDK 也变成了 Oracle JDK，自然 Oracle JDK 就是 Java SE 的闭源实现。**



> **[OpenJDK](https://blog.csdn.net/lywstuding/article/details/121117136)**
> OpenJDK 每六个月提供一次新功能发布，每三个月提供一次基于每个活动版本的维护 / 安全更新。安全更新发布日期通常是最接近 1 月，4 月，7 月和 10 月的第 17 天的星期二。
> 参考来源：https://adoptium.net/zh-CN/support/



> 这里需要注意的是，Oracle 一直直接掌控着 OpenJDK 的漏洞管理，
> 专门成立了一个 漏洞修复组，并且要求在 Oracle 发布漏洞修复程序之前，他们不会发布该漏洞。
> 由此可见，Oracle 公司自己家的护城河挖得很深，只有等 Oralce 内部修复之后，开源社区才会发布该漏洞。
> 参考来源：https://openjdk.org/groups/vulnerability/report



> OpenJDK 中 JDK GA/EA Builds 是什么意思呢？
> GA ：General availability is the phase of the software release life cycle where the software is made available for sale.
> 一般可用性是软件发布生命周期的一个阶段，在这个阶段，软件可供销售。
>
> GA Release ：General Availability Release
> 可供商用的版本，正式发布的大的版本
>
> EA Release：Early-Access Release
> 最近早期通过发布的版本，例如，JDK 20 还没正式 GA 版本，但是有早期 EA 版本
>
> BPR ：Bundled Patch Release
> 补丁版本



这里说的 OpenJDK 是 OpenJDK 开源社区中 jdk 项目，遵循 GPL v2 + ClassPath Exception 开源协议

---



### 各种开源协议的区别

**对于常用的开源协议，可以从下图了解之间的区别（乌克兰程序员 Paul Bagwell 创作的一幅流程图）：**
OSI 开源组织官网：https://opensource.org/
  ![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/f4deb61a7d1d463397987fecfefc21cb.png)



目前基于 OpenJDK 构建发行的下游 JDK 版本主要有以下这些，2009 年，在 Oracle 宣布对 JDK 商用全部收费之后，各厂商为节约成本，都开始根据开源的 OpenJDK 为自己的云服务构建自己的 JDK。

也都模仿了 Oracle 的商业收费模式（*Oracle 是根据 OpenJDK 社区免费构建仅支持 6 个月更新的 Oracle OpenJDK，同时构建商业收费的 Oracle JDK，就是我们之前常用的 JDK，商用收费的 Oracle JDK 会持续维护更新。* ），构建自己的免费的社区版 OpenJDK，然后再提供收费的商业发行版 OpenJDK（主要是提供性能优化、维护更新、漏洞补丁更新、客户问题解决等商业服务收费）:



### **OpenJDK**、**OpenJDK 免费构建版 ** 和 **Oracle JDK** 以及其它 **商业发行版** 区别

了解更多 **OpenJDK**、**OpenJDK 免费构建版 ** 和 **Oracle JDK** 以及其它 **商业发行版** 区别，参考来源及下面介绍：
1、[什么是 OpenJDK ?](https://www.azul.com/blog/what-is-openjdk/)
2、在 Oracle 内幕官网 https://inside.java/ 中的 Newscast （Java 内幕新闻）有提到，相关翻译之后的链接地址，这位 Up 主有许多系列的翻译，感觉特别给力：
[Java 内幕新闻 28 [熟肉 \]- 究竟什么是 OpenJDK](https://www.bilibili.com/video/BV1CB4y1H7Jc?spm_id_from=333.999.0.0&vd_source=2de2fd425c599791335887ce07d2f1ff)
3、来自 **腾源会** 视频号的直播回放，有 OpenJDK 社区的 [Committer](https://openjdk.org/guide/#becoming-a-committer)（*OpenJDK 开源社区角色，可以提交自己对 OpenJDK 的优化代码 *）和 [Reviewer](https://openjdk.org/guide/#becoming-a-committer) （* 比 Committer 权限更高，不但可以提交代码，还能进行审批代码 *）对 OpenJDK 开源版和商业发行版是否冲突 的一些看法和总结：[OpenJDK 开源版 和商业版 是否冲突](https://www.bilibili.com/video/BV1WB4y197b1?spm_id_from=333.999.0.0&vd_source=2de2fd425c599791335887ce07d2f1ff)
4、这位博主总结的也不错，可以参考借鉴：[码农飞升记 - 03-OpenJDK 是什么？](https://www.cnblogs.com/HeavenZhi/p/14748206.html#:~:text=OpenJDK 是 Java 平台标准版 (Java SE) 的免费开源实现。 这是，Sun Microsystems (以下简称：Sun) 于 2006 年开始努力的结果。 该实现已获得 GNU 通用公共许可证 （GNU GPL）版本 2 的许可。)



---

### OpenJDK 的版本演变

OpenJDK 还有一点值得注意的是，OpenJDK 的发展来源，是从哪个版本演变而来的呢？如下图所示：
官方参考来源：https://openjdk.org/projects/jdk6/
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/fbcc679dbf0b445695d94a663d265d54.png)

> The OpenJDK 6 code base is distinct from the code base for JDK 7 and the 6 update train. As shown in the diagram below, the original JDK 6 code base begat two lines of heirs: JDK 7 and the sequence of 6 update releases. The decision to open source the JDK code base came late in the life cycle of the development of JDK 6 so a build of JDK 7 was the first to be published as open source. By the time JDK 7 was published as OpenJDK 7, the first post-GA update to JDK 6, 6u1, had already shipped. Work continued in both the OpenJDK 7 and 6 update trains. However, an open source implementation of the Java SE 6 specification was needed as well. After considering the alternatives, OpenJDK 7 build 20 was chosen as the basis of a backward branch to create OpenJDK 6 by removing from the OpenJDK 7 sources changes inappropriate for a Java SE 6 implementation. Since then, all three trains, 6 update, OpenJDK 7, and OpenJDK 6, have continued evolving, with certain fixes being applied to or ported between the releases. Security fixes are applied to all release trains.

OpenJDK 6 代码库与 JDK 7 和 6 更新序列的代码库不同。如下图所示，原始的 JDK 6 代码库包含两行继承者：JDK 7 和 6 个更新版本序列。开源 JDK 代码库的决定是在 JDK 6 开发的生命周期后期做出的，因此 JDK 7 的构建是第一个作为开源发布的版本。当 JDK 7 作为 OpenJDK 7 发布时，对 JDK 6（6u1）的第一次 GA 后更新已经发布。OpenJDK 7 和 6 更新列车的工作仍在继续。但是，还需要 Java SE 6 规范的开源实现。在考虑了替代方案之后，OpenJDK 7 build 20 被选为向后分支的基础，通过从 OpenJDK 7 源代码中删除不适合 Java SE 6 实现的更改来创建 OpenJDK 6。从那时起，所有三列火车，6 update，OpenJDK 7 和 OpenJDK 6 都在不断发展，某些修复程序被应用到版本之间或在版本之间移植。安全修补程序适用于所有发布版本。



**总结：**

可以看出，2006 年 11 月 13 日 Sun 公司宣布将 Java 开源，2006 年 12 月 12 日 Sun 公司发布了 Java SE 6 版本。

2006 年 11 月成立了 OpenJDK 开源组织，2007 年 3 月起，全世界用户可以对 Java 源代码进行修改。在这个时间节点上，可以看出，2006 年 11 月 13 日 开源 JDK 代码库的决定是在 JDK 6 开发周期后期提出的（这个时候 JDK 6 开发快要结束了，同年 12 月 12 日发布了 JDK 6 可以看出），不可能立马把 JDK 6 构建成开源发布版本。而另一条 JDK 7 的研发线才刚刚开始，所以 JDK 7 的构建是第一个作为开源发布的版本。

当 JDK 7 作为 OpenJDK 7 发布时，JDK 6 GA（正式商业版本）版本 之后的 更新版本 JDK 6u1 也已经发布了，JDK 6 已经更新迭代了很多版本了。

现在还差 JDK 6 的开源实现，考虑了替代方案之后，OpenJDK 7 build 20 被选为向后分支的基础，通过从 OpenJDK 7 源代码中删除不适合 Java SE 6 实现的更改来创建 OpenJDK 6。

在 2009 年 Oracle 收购 Sun 公司之后（在收购之前 2 年，OpenJDK 7 延续着 JDK 7 的开发路线，走在了 JDK 7 前面，**猜想：在收购前，Sun 公司没必要继续研发 JDK 7 ，毕竟已经开源了，只需要研发维护 OpenJDK 7 即可，不然 Sun 开源 Java，自己再起个小灶，也就没有什么意义了。**），一直到 2011 年，Oracle 才发布 Oracle JDK 7 GA 版本。

到 2009 年 Oracle 收购 Sun 公司，此时的 JDK 7 还在 b10 版本，而 OpenJDK 已经延续迭代更新了多个版本，Oracle 收购 Sun 公司，获得了 Java 的 拥有权，明显是看中了 Java 的商业价值，奔着收费去的。当时停滞的 JDK 7 为了发行 GA 版本，只能参考 OpenJDK 的代码实现。而且大家还不太信任 Oracle，所以 Oracle 不能立马对 Java 收费，但是它可以暂时几乎大部分免费，只有少部分商业特性收费。

后面一直持续到 2019 年 4 月 16 日，当大部分用户对 Oracle JDK 产生了依赖时，特别是 中小企业，就开始收割韭菜要收费了。

所以说 Oracle JDK 7 的参考实现 完全基于 OpenJDK 7 源代码，2 者之间的代码功能几乎没什么区别，当然后面的 OpenJDK 社区 是由 Oracle 领导，成为社区的主要的贡献者，并且延续了 OpenJDK 快速迭代，Oracle JDK 稳定测试、吸收、优化发布 GA 版本。

由于 OpenJDK 更新迭代的非常快，所以从 Oracle JDK 7 开始，后面 Oracle JDK 的参考实现 完全基于 OpenJDK 。

---

### 基于 OpenJDK 的开源版本



#### [Oracle JDK](https://www.oracle.com/java/technologies/downloads/archive/)

2009 年 Oracle 收购 Sun 公司之后，原来的 Sun JDK 也变成了 Oracle JDK。Oracle JDK 现在的版本更新完全基于 OpenJDK 实现 ，目前的 Oracle JDK 为了加快 新功能的引入， 每 6 个月更新一个大的版本，每 3 个月进行一次 维护 / 安全 补丁更新，目前每 2 年发布一个长期支持版本。
Oracle JDK 从 2022 年 4 月开始，重要补丁更新将在 1 月、4 月、7 月和 10 月的第三个星期二发布（它们之前发布在最接近 1 月 、4 月、7 月和 10 月的第 17 天的星期二）

- **Oracle JDK 安全补丁更新周期 **，参见：https://www.oracle.com/security-alerts/
- **Oracle JDK 支持路线图 **，参见：https://www.oracle.com/java/technologies/java-se-support-roadmap.html
- **Oracle JDK 以及 Oracle OpenJDK 使用的许可证 **，参见：https://www.oracle.com/java/technologies/javase/jdk-faqs.html
- **Oracle 为 OpenJDK 社区贡献了以下功能：**
  1. [Java Flight Recorder](http://openjdk.java.net/jeps/328)
  2. [Java Mission Control](http://openjdk.java.net/projects/jmc/)
  3. [Application Class-Data Sharing](http://openjdk.java.net/jeps/310)
  4. [ZGC](http://openjdk.java.net/jeps/333)
     因此，从 Java 11 开始，**Oracle JDK** 构建 和 **OpenJDK** 构建 将基本相同。
     … 但有一些外观和包装差异
     确实仍然存在少量差异，有些是有意的和装饰性的，有些只是因为需要更多时间与 OpenJDK 贡献者讨论。
     具体差异细节参见：https://blogs.oracle.com/java/post/oracle-jdk-releases-for-java-11-and-later

---



#### [Oracle OpenJDK](https://jdk.java.net/)

自 2017 年 9 月 JDK 9 发布开始，Oracle 在免费的开源许可证（GPL v2 + CPE)（类似于 Linux）下提供 OpenJDK 二进制构建版本。Oracle 提供的 OpenJDK 版本的可用性和社区支持在 [jdk.java.net](https://jdk.java.net/) 上单独列出。

从 Java 11 开始，**Oracle JDK** 构建 和 **OpenJDK** 构建 将基本相同。

Oracle 基于 OpenJDK 构建的 Oracle OpenJDK，更新周期只有 6 个月，在这 6 个月更新期间，Oracle OpenJDK 和 Oracle JDK 几乎没什么区别。6 个月之后，原来版本的 Oracle OpenJDK 不再维护更新，Oracle 会重新构建新版本的 Oracle OpenJDK。

Oracle OpenJDK 可以理解为 Oracle JDK 的试用版或者尝鲜版。

---



#### [Eclipse Temurin](https://adoptium.net/zh-CN/)

**Eclipse Temurin 是 Adoptium OpenJDK**（由原来的 [AdoptOpenJDK](https://adoptopenjdk.net/) 迁移而来，2021 年 7 月 24 日，AdoptOpenJDK 加入了 Eclipse 基金会并重塑品牌）**发行版的名称**
[Eclipse Temurin 永远免费 提供可执行二进制文件](https://adoptium.net/zh-CN/docs/faq/index.zh-cn/)，由于 IBM 收回了 OpenJ9 (最开始 Open J9 是 IBM 免费开源贡献给 Eclipse 的，后来被收回了，命名为 [IBM Semeru](https://developer.ibm.com/languages/java/semeru-runtimes/))，所以不再继续分发 Eclispse OpenJ9 的构建。
Eclipse Temurin 的更新周期依然延续 AdoptOpenJDK 的。
[Eclipse Temurin 的更新周期维护路线](https://adoptium.net/zh-CN/support/) 如下图所示，Eclipse Temurin 比 Oracle OpenJDK 维护更新的周期时间更长，针对于 Java 长期支持版本 提供至少 4 年的更新维护 ：
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/45febe84e0df4b01a503c67f0c04576f.png)
Eclipse Temurin 也与 部分供应商合作，建立了 [Adoptium™ Marketplace](https://adoptium.net/zh-CN/marketplace?version=8) ，主要用来提供用户 免费下载 各厂商的符合标准的构建的 OpenJDK 免费二进制文件，如下图所示：
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/13c77e64561246c48e6d8110fa69fb91.png)



---

#### [AdoptOpenJDK](https://adoptopenjdk.net/)
**[谁是 AdoptOpenJDK.net 社区的幕后推手？](https://adoptopenjdk.net/about.html?variant=openjdk11&jvmVariant=openj9)**
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/d88b5b46b2da4b799f9e7ed50d5eea44.png)
就像官网所说那样，AdoptOpenJDK 是由 Java 用户组成员，Java 开发者 以及 供应商（有 Azul, Amazon, GoDaddy, IBM, jClarity (acquired by Microsoft), Microsoft, New Relic, Pivotal and Red Hat)组成的社区，可以看出 **AdoptOpenJDK 有 OpenJDK 社区 官方成员 和 供应商支持，可以说专门针对于 OpenJDK 社区 中 OpenJDK 进行构建二进制可执行文件，属于直接对 OpenJDK 的原始构建，不像其它下游 OpenJDK 商业发行版 有各自的争对性定制化的优化。**



[如何获得商业支持的 AdoptOpenJDK binaries（可执行的二进制文件）？](https://adoptopenjdk.net/faq.html?variant=openjdk11&jvmVariant=openj9)
![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/2956875ddb2b42c78f8b12cd35546123.png)



AdoptOpenJDK 不提供商业支持版，AdoptOpenJDK 中的 OpenJDK 代码本身 同样也是 GPL v2 + CPE 协议的。
AdoptOpenJDK 使用 [基础架构项目](https://github.com/AdoptOpenJDK/openjdk-infrastructure) ，[构建项目](https://github.com/AdoptOpenJDK/openjdk-build) 和 [测试脚本](https://github.com/AdoptOpenJDK/openjdk-tests) 从 OpenJDK™类库生成预构建的二进制文件，并且可以选择 OpenJDK 或 Eclipse OpenJ9 VM 虚拟机（现在的 OpenJ9 虚拟机 已被 IBM 收回，无法提供最新的版本）。所有 AdoptOpenJDK 二进制文件和脚本都是开源许可和免费提供的。



![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/fc60330f40ce4c669948d33f60283848-20220811184633427.png)

---

#### 其它下游 OpenJDK

##### [Alibaba Dragonwell](https://www.aliyun.com/product/dragonwell)

Alibaba Dragonwell 是一款免费的，生产就绪型 Open JDK 发行版，提供长期支持，包括性能增强和安全修复。阿里巴巴拥有最丰富的 Java 应用场景，覆盖电商，金融，物流等众多领域，世界上最大的 Java 用户之一。Alibaba Dragonwell 作为 Java 应用的基石，支撑了阿里经济体内所有的 Java 业务。GitHub 地址：https://github.com/alibaba/dragonwell8



##### [腾讯 Kona](https://cloud.tencent.com/document/product/1149/38537)

腾讯 Kona（Tencent Kona，TK）是完全开源、生产环境就绪的 OpenJDK 发行版，完全兼容开源 OpenJDK8、OpenJDK11 及 OpenJDK17。腾讯 Kona 基于 TencentJDK 开发，支持多种运行平台，同时针对云应用场景定制新的功能及优化， 具备更快的云应用启动速度，更好的性能以及提供更为便捷的分析、诊断工具。此外，腾讯 Kona 服务于腾讯内部大量 Java 应用程序，提供稳定、高效的 Java 运行环境。



##### [华为毕昇 JDK](https://www.hikunpeng.com/zh/developer/devkit/compiler/jdk)

毕昇 JDK 是华为基于 OpenJDK 优化后的开源版本，是一个高性能、可用于生产环境的 OpenJDK 发行版。毕昇 JDK 运行在华为内部多个产品上，积累了大量使用场景和 Java 开发者反馈的问题和诉求，解决了业务实际运行中遇到的多个问题，并在 ARM 架构上进行了性能优化。毕昇 JDK 运行在大数据等场景下可以获得更好的性能。毕昇 JDK 8 与 Java SE 标准兼容，目前支持 Linux/AArch64 和 Linux/x86_64 平台。毕昇 JDK 同时是 OpenJDK 的下游，会持续稳定为 OpenJDK 社区做出贡献。



##### [Amazon Corretto](https://aws.amazon.com/cn/corretto/?filtered-posts.sort-by=item.additionalFields.createdDate&filtered-posts.sort-order=desc)

Amazon Corretto 是开放 Java 开发工具包 (OpenJDK) 的免费、多平台、生产就绪型发行版。Corretto 提供长期支持，其中包括性能增强和安全修复。亚马逊在内部的数千种生产服务上运行 Corretto，并且 Corretto 已被证明能够兼容 Java SE 标准。借助 Corretto，您可以在常用操作系统（包括 Linux、Windows 和 macOS）上开发和运行 Java 应用程序。



##### [Red Hat build of OpenJDK](https://developers.redhat.com/products/openjdk/overview)

OpenJDK 的 Red Hat 版本是 Java Platform，Standard Edition（Java SE）的免费开源实现。红帽现在又接管了 OpenJDK 8、OpenJDK 11 更新项目，负责领导 OpenJDK 8 和 11 的长期更新。红帽每年通过 rpm 和 zip 文件为 OpenJDK 8 和 11 发行版提供季度 JRE 和 JDK 更新。
参考网址：

1. [红帽 与 OpenJDK 之间的历史关系](https://www.redhat.com/en/blog/history-and-future-openjdk)
2. [红帽对 OpenJDK 支持的生命周期](https://access.redhat.com/articles/1299013)



##### [Azul Zulu Builds of OpenJDK](https://www.azul.com/downloads/?package=jdk#download-openjdk)

Azul 公司官网上宣传的说是业界支持 Java 最好的，达到或超过 Oracle 的产品版本，基于 8+2 年产品生命周期模型的长期支持 （LTS）。受到 27% 的财富 100 强企业信赖，1 亿次安装。全球最大的 OpenJDK 提供商，作为世界上唯一一家专注于 Java 的公司。
关于 Azul Support for OpenJDK FAQ：https://www.azul.com/azul-support-for-openjdk-faq/



##### [LIBERICA JDK](https://bell-sw.com/pages/libericajdk/)

Liberica JDK 是由 BellSoft 公司开发的 Java™ 平台的免费开源实现，充满了有价值的功能和增强的支持。
BellSoft 公司由 Alex Belokrylov，Gregory Labzovsky 和 Alexei Voitylov 创立，他们之前曾在 Oracle 工作。他们聘请了 Java 平台的关键开发人员和从 1.5 开始的所有 Java 版本的贡献者。
BellSoft 公司被 Oracle 首席产品经理 Dalibor Topic 和 Java 开发人员关系总监 Sharat Chander 评为 Java 开发领域的领先公司之一。
2020 年，BellSoft 公司 成为 OpenJDK 社区 的活跃成员，上游 Arm 端口的维护者和 OpenJDK 贡献者。该公司进入了前 5 名的 OpenJDK 贡献者。并且 BellSoft 公司 成为了 JCP 的成员。
关于 Liberica JDK 背景介绍：https://bell-sw.com/pages/about/



##### [SapMachine](https://sap.github.io/SapMachine/)

SapMachine 是由德国软件公司 SAP（思爱普）维护和支持的 OpenJDK 下游版本。它用于为 SAP 客户和希望使用 OpenJDK 运行应用程序的合作伙伴构建和维护一个 SAP 支持的 OpenJDK 版本。自 2001 年以来 SAP 一直是 JCP 执行委员会成员，是 OpenJDK 项目最大的外部贡献者之一。领导 OpenJDK 17 更新项目，大力支持 OpenJDK 11 更新项目。



##### [OpenLogic OpenJDK](https://www.openlogic.com/)

OpenLogic 公司 也是 OpenJDK 开源社区贡献者之一，2019 年 8 月，OpenLogic 认识到 OpenJDK 中 Java WebStart 技术的当前状态不如 Oracle JDK 同等技术。该构建缺少关键功能，例如站点异常、jar 密钥匹配、Windows 代理设置和其他功能。
[OpenLogic 为 IcedTea-Web 添加了功能，以实现与 Oracle 的 WebStart 发行版相同的功能。IcedTea-Web 2.0 接受了这些升级。](https://www.openlogic.com/open-source-java-services-and-support?utm_medium=email&utm_source=cross-sell-database&utm_campaign=2020-openlogic-webinar-top-tips-cost-savings-open-source&utm_content=webinar)
OpenLogic 提供并支持 OpenJDK 8 的免费发行版，以及适用于 Linux、Windows、MacOS 和 Docker 映像的免费、经过认证的 OpenJDK 11 发行版。OpenLogic 的认证 OpenJDK 版本每季度更新一次，并按需提供关键安全补丁。
OpenLogic 还为所有 Java 发行版提供商业支持，包括 Adopt OpenJDK、IBM 和 Oracle 的 Java。OpenLogic 的 Java 支持包括安全补丁和错误修复，以及 Java 和 JVM 的使用和管理指南。
OpenLogic 构建的 OpenJDK 是一种替代方案



##### [GraalVM](https://www.graalvm.org/22.2/docs/introduction//)

GraalVM 是一个在 HotSpot 虚拟机基础上增强而成的跨语言 ** 全栈虚拟机 **，可以作为 “任何语言” 的运行平台使用 ，旨在加速用 Java 和其他 JVM 语言编写的应用程序的执行，同时还为 JavaScript、Python 和许多其他流行语言提供运行时。GraalVM 提供了两种运行 Java 应用程序的方法：在 HotSpot JVM 上使用 Graal 即时 (JIT) 编译器或作为提前 (AOT) 编译的本机可执行文件。GraalVM 的多语言功能可以在单个应用程序中混合多种编程语言，同时消除外语调用成本。

**GraalVM 架构如下图所示:![在这里插入图片描述](https://raw.githubusercontent.com/mapleincode/images/master/uPic/fbf048cc2e534c2994d48ebd3a0939d0.png)

#### 


### 疑问点

**为什么有些基于 OpenJDK 构建发行的 JDK 没有开放源代码？**

Oracle 想通过之前基本免费的 JDK，产生了大量用户，然后中间收费捞一笔。一些大的厂商，特别是在自己的云服务战略上，如果使用收费的 JDK，成本会非常高，导致云计算建设失去了用户低成本性。

所以对比那些有云计算服务的厂商必须研发自己的 JDK，不但可以节约成本，而且还可以及时的为云客户提供更新维护服务。

之前的 OpenJDK 开源社区 主要只有 Oracle 公司领导以及部分社区成员，大大影响了 JDK 的成长。

2019 年 Oracle 收费之后，各大厂商，例如 IBM、红帽、Azul、亚马逊、微软、阿里巴巴、腾讯、华为等企业
都参与到 OpenJDK 开源的建设中来，把自己下游的 JDK 发行版的 一些优化补丁提供给 OpenJDK 开源社区，
这是一种好的现象，虽然说现在的 JDK 版本烟花缭乱，但是 JDK 的成长发展，必须要大家一起来努力，
就像 以前的 Linux 系统一样。

Oracle 自己可能也没有想到，自己的全面商用收费，带来了 OpenJDK 开源社区的繁荣，大家都参与了进来。
不过从用户的角度以及一些中小企业（中小企业本身没有太多实力和成本来研发自己的 JDK ）来说，
对 JDK 版本的选择变得更加困难。需要根据自己业务需求，去比较选择合适的 JDK 发行版。

当然，也许是 Oracle 公司 自己 一家 无法承担 OpenJDK 开源社区 的所有研发成本。它必须要把一些业务抛出去，让大家参与社区开源项目，减少维护成本。从 Oracle 公司 陆续 把 OpenJDK 6、OpenJDK 7、OpenJDK 8、OpenJDK 11 的后续更新维护，移交给 红帽公司，就可以看出，Oracle 公司在一步步减负前行，可能是为了自己的云计算服务来考量的。

微软之前极力反对开源，但是自从收购 GitHub 之后，可以看出，微软正在积极拥抱开源，发现了开源所带来的的巨大的经济效益。

IBM 也收购了 红帽公司（通过 Linux 发行版提供维护收费服务），已经开始进军云计算领域。

现在的 OpenJDK 开源社区 研发进度得到了极大的提升。



## 5. 关于 Oracle JDK 商用收费和后续更新

OBCL（Oracle Binary Code License）：之前的部分收费协议（部分商业特性使用需要收费，其它可以免费商用）
OTNL（ Oracle Technology Network License）：商用收费协议
ONFT（ Oracle No-Fee Terms and Conditions License）：从 JDK 17（包括 JDK 17）开始出现的短暂免费协议

---

### JDK 是从哪个版本开始商用全部收费的呢?

  [Oracle JDK 各版本下载链接](https://www.oracle.com/java/technologies/downloads/archive/)

 ***JDK 8u211** 版本（包括 JDK 8u211 版本）开始，Oracle 开始商用全部收费，遵循 OTNL（ Oracle Technology Network License）协议。*



**JDK 8u202**（包括 JDK 8u202）之前的版本，都是 **大部分商用免费 **（只有部分商业特性需要收费，参见：https://www.oracle.com/java/technologies/se-products.html ）的。遵循 **BCL**（Oracle Binary Code License）协议。[官方详情链接](https://www.oracle.com/java/technologies/javase/javase8-archive-downloads.html)<——



2019 年 4 月 16 日 Oracle 针对 JDK 后续版本发布了新的协议 

OTNL（ Oracle Technology Network License），从此 JDK 8u211 及后续 JDK 版本开始商用全部收费。[官方详情链接](https://www.oracle.com/java/technologies/javase/javase8u211-later-archive-downloads.html)

### JDK 17 开始使用了新的协议

**NFTC（Oracle No-Fee Terms and Conditions License）**

>   Oracle JDK 17 and future JDK releases are provided under a free-to-use license until a full year after the next LTS release.

大概翻译如下（[官方详情链接](https://www.oracle.com/news/announcement/oracle-releases-java-17-2021-09-14/)）：

>   Oracle JDK 17 和未来的 JDK 版本是在免费使用许可下提供的，直到下一个 LTS 版本发布一整年后，在这中间发布的 JDK 版本都将收费。
>   也就是说 JDK 17 之后，在下一个长期版本 JDK 21 未发布之前，这期间 JDK 17 不收费，但是下一个长期版本 JDK 21 发布之后 ，JDK 17 开始需要收费。总之就是变相的收费。

### Oracle JDK 版本更新路线图

  [官方 Oracle Java SE 支持路线图链接](https://www.oracle.com/java/technologies/java-se-support-roadmap.html)
  Java SE 7、8、11 和 17 是 LTS 版本。Oracle 打算每两年发布一次未来的 LTS 版本，这意味着下一个计划的 LTS 版本是 2023 年 9 月的 Java 21。那么前面发布的 JDK17 到 JDK20 就会开始收费。

  JDK 当前支持更新版本日期信息如下表，[参考链接](https://dev.java/download/releases/)：



#### Currently Supported Releases

| Version | Initial Release | Current Release    | Version Info                                                 | End of Life |
| ------- | --------------- | ------------------ | ------------------------------------------------------------ | ----------- |
| 18      | 2022-03-22      | 18.0.1 2022-03-22  | [Release Notes ](https://jdk.java.net/18/release-notes)[Documentation ](https://docs.oracle.com/en/java/javase/18/docs/api/)[Certified Configurations ](https://www.oracle.com/java/technologies/javase/products-doc-jdk18certconfig.html)[Open JDK Project Page ](https://openjdk.java.net/projects/jdk/18)[JSR 393](https://jcp.org/en/jsr/detail?id=393) | 2022-09-20  |
| 17      | 2021-09-14      | 17.0.3 2022-01-18  | [Release Notes ](https://www.oracle.com/java/technologies/javase/17-relnotes.html)[Documentation ](https://docs.oracle.com/en/java/javase/17/docs/api/)[Certified Configurations ](https://www.oracle.com/java/technologies/javase/products-doc-jdk17certconfig.html)[Risk Matrix ](https://www.oracle.com/security-alerts/cpujan2022.html#AppendixJAVA)[Open JDK Project Page ](https://openjdk.java.net/projects/jdk/17)[JSR 392](https://jcp.org/en/jsr/detail?id=392) | 2029-09-30  |
| 11      | 2018-09-25      | 11.0.14 2022-01-18 | [Release Notes ](https://www.oracle.com/java/technologies/javase/11-0-11-relnotes.html)[Documentation ](https://docs.oracle.com/en/java/javase/11)[Certified Configurations ](https://www.oracle.com/java/technologies/javase/products-doc-jdk11certconfig.html)[Risk Matrix ](https://www.oracle.com/security-alerts/cpuApr2021.html#AppendixJAVA)[Open JDK Project Page ](https://openjdk.java.net/projects/jdk/11)[JSR 384](https://jcp.org/en/jsr/detail?id=384) | 2026-09-30  |
| 8       | 2014-03-18      | 8u311 2021-10-19   | [Release Notes ](https://www.oracle.com/java/technologies/javase/8u291-relnotes.html)[Documentation ](https://docs.oracle.com/javase/8)[Certified Configurations ](https://www.oracle.com/java/technologies/javase/products-doc-jdk8-jre8-certconfig.html)[Risk Matrix ](https://www.oracle.com/security-alerts/cpuApr2021.html#AppendixJAVA)[JSR 337](https://jcp.org/en/jsr/detail?id=337) | 2030-12-31  |
| 7       | 2011-07-11      | 7u321 2021-10-19   | [Release Notes ](https://www.oracle.com/java/technologies/javase/7-support-relnotes.html#R170_301)[Documentation ](https://docs.oracle.com/javase/7)[Certified Configurations ](https://www.oracle.com/java/technologies/jdk-jre-7-cs-config.html)[Risk Matrix ](https://www.oracle.com/security-alerts/cpuApr2021.html#AppendixJAVA)[JSR 336](https://jcp.org/en/jsr/detail?id=336) | 2022-07-19  |

#### Future Releases

| Version | Initial Release | Version Info                                                 |
| ------- | --------------- | ------------------------------------------------------------ |
| 19      | 2022-09-20      | [Open JDK Project Page](https://openjdk.java.net/projects/jdk/19) |