---
title: zookeeper 笔记
date: 2022-08-03 15:51:29
tags: zookeeper
---



[toc]

## 命令行

- ls / ls2
- delete
- set
- get
- stat



## 节点持久类型

```bash
PERSISTENT			// 持久节点，一旦创建成功不会被删除，除非客户端主动发起删除请求
PERSISTENT_SEQUENTIAL		// 持久顺序节点，会在用户路径后面拼接一个不会重复的自增数字后缀，其他同上
EPHEMERAL			// 临时节点，当创建该节点的客户端链接断开后自动被删除
EPHEMERAL_SEQUENTIAL		// 临时顺序节点，基本同上，也是增加一个数字后缀
CONTAINER			// 容器节点，一旦子节点被删除完就会被服务端删除
PERSISTENT_WITH_TTL		// 带过期时间的持久节点，带有超时时间的节点，如果超时时间内没有子节点被创建，就会被删除
PERSISTENT_SEQUENTIAL_WITH_TTL	// 带过期时间的持久顺序节点，基本同上，多了一个数字后缀
```



## ACL

zookeeper 的 acl 通过 **[ scheme : id : permissions ]** 来构成权限列表。

- **scheme**：代表采用的某种权限机制，包括 world、auth、digest、ip、super 几种。
- **id**：代表允许访问的用户。
- **permissions**：权限组合字符串，由 **cdrwa** 组成，其中每个字母代表支持不同权限
  - 创建权限 create (c)
  - 删除权限 delete (d)
  - 读权限 read (r)
  - 写权限 write (w)
  - 管理权限admin (a)。


### Acl 命令

- setAcl
- getAcl



### world 实例

```
$ getAcl /runoob/child
$ setAcl /runoob/child world:anyone:crwa
$ delete /runoob/child
```



直接操作 `world:anyone` 即可。即默认节点权限。

### auth 实例

auth 实例用于授权。

> 授权需要新建用户

```shell
$ addauth digest user1:123456 # 创建用户
$ setAcl /runoob/child auth:user1:123456:cdrwa # 授权
$ getAcl /runoob/child # 获取 acl 内容

# 'digest,'user1:HYGa7IZRm2PUBFiFFu8xY2pPP/s=
# : cdrwa
```



这里 digest 相当于是创建也相当于手授权。这时候退出终端，再访问 `/runoob/child` 就会返回 No Auth 报错

```shell
[zk: localhost:2181(CONNECTED) 0] get /runoob/child
org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth for /runoob/child
```

再通过 `addauth digest user1:123456` 验证之后就可以正常访问。



### digest 实例

在 auth 对 Node 进行了授权，授权需要用户的密码。

但是如果是管理员，能否在不知道用户的密码的前提下对 Node 进行授权？

答案是可以的。

这里注意 `getAcl /runoob/child`  返回了

```shell
'digest,'user1:HYGa7IZRm2PUBFiFFu8xY2pPP/s=
: cdrwa
```



使用 digest 进行授权:

```shell
$ create /runoob/child01 runoob
$ getAcl /runoob/child01
$ setAcl /runoob/child01 digest:user1:HYGa7IZRm2PUBFiFFu8xY2pPP/s=:cdra
$ getAcl /runoob/child01
$ addauth digest user1:123456
$ getAcl /runoob/child01
```



> 区别于 auth 需要用户的密码，digest 授权更适合管理员进行操作。在已知用户密码加密后的内容之后，也可以对其他的 node 进行授权。



### IP 实例

> 利用 ip 进行授权

```shell
$ create /runoob/ip 0
$ getAcl /runoob/ip
$ setAcl /runoob/ip ip:192.168.3.7:cdrwa
$ get /runoob/ip
```



## 用 zk 实现非公平锁

本质就是依赖 zk Node 只能被同时创建一次来实现。

- 创建 Node
  - 成功
  - 失败
    - 监听 NodeDeleted 事件
      - 继续创建 Node
        - 成功
        - 失败 
          - 继续监听...

EPHEMERAL 临时节点可以避免客户端断开链接但是未删除造成的死锁。

> 临时节点在链接断开之后自动删除节点



## 用 zk 实现公平锁

依赖 EPHEMERAL_SEQUENTIAL 的特性。

所有竞争者都去 Node 上创建，并且获得顺序的唯一 id。

 创建完就去获得 Node 列表中第一个 Node。如果 Node 相同说明获得锁，反之说明获得锁失败。



如果失败，就去监听当前自己获得的节点的上一个。这样一旦前者被删除了，说明自己也就可以了。



这里有个情况是，如果前者因为其他原因被删除，比如说，链接断开？

那就重复去查询列表第一个 Node，如果自己不是第一个，那就监听自己当前的前一个节点。



## curator-recipes

```xml
<dependency>
  <groupId>org.apache.curator</groupId>
  <artifactId>curator-recipes</artifactId>
  <version>5.1.0</version>
</dependency>
```



```java
RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
CuratorFramework client = CuratorFrameworkFactory.newClient("127.0.0.1:2181", retryPolicy);
client.start();
InterProcessMutex lock = new InterProcessMutex(client, "/lock");
try (Locker locker = new Locker(lock)) {
  // 使用 try-with-resources 语法糖自动释放锁
  System.out.println("获取到锁后的业务逻辑");
}
client.close();
```



- `InterProcessMultiLock` 可以同时对几个路径加锁，释放也是同时的
- `InterProcessMutex` 可重入排他锁
- `InterProcessReadWriteLock` 读写锁
- `InterProcessSemaphoreMutex` 不可重入排他锁



## Wathcer

> 研究的是 Java SDK 里的方法

注册 watcher 有三种方式：

- getData
- exists
- getChildren



而通过源码查看了 EventType 有 8 种：

```java
None(-1),
// 其他的消息

// 对应 exists
NodeCreated(1),
NodeDeleted(2),

// 对应 getChildren
NodeDataChanged(3),

// 对应 getData
NodeChildrenChanged(4),

// exists 和 getData 某路径的监听被移除；实际 watcher 依然存在
// client.removeWatches("/", watcher, Watcher.WatcherType.Data, true);
DataWatchRemoved(5),

// getChildren 某路径的监听被移除； watcher 依然存在
// client.removeWatches("/", watcher, Watcher.WatcherType.Children, true);
ChildWatchRemoved(6),

// 永久设置 watcher 被移除
// client.addWatch("/", watcher, AddWatchMode.PERSISTENT);
// client.removeWatches("/", watcher, Watcher.WatcherType.Any, true);
PersistentWatchRemoved(7)
```

> 

watcher 对象相当于一个监听者，可以通过在 client 只设置一个通用 watcher 的办法，然后通过类似

```
client.exists("/",  true);
```

的办法，把 watcher 绑定到对应的路径和方法。

> 除了需要的 EventType , None 类型的事件也会被监听到，所以需要过滤。
>
> 除了 None 之外，其他类型的 EventType 只会在设置后被监听到一次，所以在处理完事件之后，需要重新进行设置。



#### Persistent Recursive Watcher

A managed persistent persistent watcher. The watch will be managed such that it stays set through connection lapses, etc.

从定义上来看，即使与  zk 断开，依然保持链接。

> 客户端发送请求给服务端是通过 TCP 长连接建立网络通道，底层默认是通过 java 的 NIO 方式，也可以配置 netty 实现方式。

默认监听路径里所有的事件。

```java
client.addWatch("/", watcher, AddWatchMode.PERSISTENT); // 添加监听
client.removeWatches("/", watcher, Watcher.WatcherType.Any, true); // 删除监听
```



## 参考

- https://github.com/HelloGitHub-Team/HelloZooKeeper
- https://www.runoob.com/w3cnote/zookeeper-acl.html
- https://curator.apache.org/curator-recipes/persistent-watcher.html

