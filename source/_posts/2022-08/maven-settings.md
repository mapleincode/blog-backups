---
title: maven settings.xml 笔记
date: 2022-08-04 10:43:29
tags: [maven]
---



Maven 常见的配置文件由以下几部分：

- proxy
- mirror
- repository
- server
- profiles



其中 proxy 就是代理服务，这一点暂时不管。



## id

repository 和 mirror 和 server 都拥有一个字段是 id。这里的 id 就单纯的用来标识一个 server 的唯一的标识字段。

我一开始以为类似 `central`, `thirdparty` 是固定的字段，后面理解开来，应该只是属于常用的 id，并没有固定的含义。



## profiles

profiles 可以认为是配置，其 id 是标识配置的唯一 id （和 server 无关）。

可以认为为了方便切换配置。

其选择通过 `activeProfiles` 字段标识。

```xml
<activeProfiles>
    <activeProfile>{profileId}</activeProfile>
 </activeProfiles>
```



profile 包含三部分：

- id，也就是 profile 的 id
- repositories
- pluginRepositories



## repositories

仓库。

包含属性：

- id，唯一标识
- url，地址
- releases & snapshots，通过版本号 version 来区分，包含 SNAPSHOT 字段的就是快照版本，否则为 release 版本



多个  repository 的情况下，根据其在 repositories 定义的先后顺序进行查找。直到查询到为止。



## pluginRepositories

插件仓库，定义方式和 repositories 相同。定义的是类似 mvn 在实现功能时的所需要的包的 repo。

如果不定义，在 mvn clean install 等操作时，依然会拉取 maven 官方库。



## mirrors

镜像。

mirrors 可能在国内用的比较多，介于网速的原因。

和 repository  相比，mirror 配置除了没有 releases 和 snapshots，还多了一个 mirrorOf。

mirrorOf 配置的需要被镜像代理的 repository 的 id。



除了 id 之外，mirrorOf 还支持以下参数：

- `*`: 匹配所有，所有内容都从镜像拉取
- `external:*`: 除了本地缓存的所有从镜像仓库拉取
- `repo,repo1`: repo 或者 repo1 ，这里的 repo 指的是  repository 的 id。
- `*,!repo1`: 除了 repo1 的所有仓库



一个 repo 最多被一个镜像代理，例如 mirrors 有两个 mirror 都代理了 id：central。那有且只有第一个 mirror 会被访问。

> 如果第一个 mirror of 了 central ，第二个 mirror of 了 *。
>
> 那在访问 id 为 central 的 repo 会访问第一个 mirror，而在访问其他 repo 时会访问第二个 mirror。

##repositories 和 mirrors 的区别

repositories 属于仓库配置，相当于定义不同的资源 （snapshot 和 release ）从哪里拉取，优先从那里拉取。

所以 repository  需要配置  snapshots 和 releases 参数。例如， releases 可以从 aliyun 优先拉取，而 snapshots 则可以设定只从公司的 repos 甚至个人私有的 repos 获取。



而 mirrors 本质更像是用来加速，比如定义了 id 为 central 的 repo，则在 mirrors 里配置加速的 mirros 里时，里面的 mirrorOf 就需要填写为 central。



两者很多功能都比较类似，感觉更像是定义上的区别。比如 maven 官方库，和 aliyun 库。

我可以把 maven 定义成 central ，而 aliyun 定义成 central-1。这样两者都是 repos，然后把  aliyun 的 repo 放在 maven 的前面，这样优先会拉取 aliyun 的仓库。



但是如果我把 maven 定义成 central，而把 aliyun 定义成 mirror，并且 mirrorOf central。这样系统只会从 aliyun 去获取资源，因为他们本质只是一个 repo。



所以，正常情况下除非是用于加速，否则不同类型的 repo 应该都设置到 repositories 去。这样一旦需要代理，直接修改 mirrors 就可以了。

> 实际上拿到同事的配置，绝大多数的配置都放在了 mirrors ，这个概念应该是错的。
>
> 正常应当把 maven 官方 repo 放在 repositories ，类似 aliyun 仓库的应当作为 central 的镜像被放到 mirros。
>
> 而各类私有仓库，应当都放到 respositories ，并且定义不同的 id。



## servers

servers 里定义的是访问私有库的账户密码。

这里比较有意思的是项目里 pom.xml  也会定义 distributionManagement。这里的 repository 需要的鉴权，也可以被定义到 `~/.m2/settings.xml` 里的 servers。



## 参考

1. https://juejin.cn/post/6993190688846266375
2. https://juejin.cn/post/6844904071518748680
3. https://blog.csdn.net/jeikerxiao/article/details/60324713