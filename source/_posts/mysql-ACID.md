---
title: MySQL 事务
date: 2018-08-29 01:18:36
tags: MySQL
---

大学上课的时候我想老师基本都讲过事务这件事，就是一旦一件事件涉及多条数据库操作，当一条操作失败时，理应当把之前的操作全部回滚。



我们最常用的 MySQL 引擎之一的 InnoDB 支持事务。

事务 ACID 指的是 Atomicity（原子性）、Consistency（稳定性）、Isolation（隔离性）、Durability（可靠性）。

1. **原子性：**一个事务（transaction）中的所有操作，要么全部完成，要么全部不完成，不会结束在中间某个环节。事务在执行过程中发生错误，会被回滚（Rollback）到事务开始前的状态，就像这个事务从来没有执行过一样。

1. **一致性：**在事务开始之前和事务结束以后，数据库的完整性没有被破坏。这表示写入的资料必须完全符合所有的预设规则，这包含资料的精确度、串联性以及后续数据库可以自发性地完成预定的工作。
2. **隔离性：**数据库允许多个并发事务同时对其数据进行读写和修改的能力，隔离性可以防止多个事务并发执行时由于交叉执行而导致数据的不一致。事务隔离分为不同级别，包括读未提交（Read uncommitted）、读提交（read committed）、可重复读（repeatable read）和串行化（Serializable）。
3. **持久性：**事务处理结束后，对数据的修改就是永久的，即便系统故障也不会丢失。



> 在 MySQL 命令行的默认设置下，事务都是自动提交的，即执行 SQL 语句后就会马上执行 COMMIT 操作。因此要显式地开启一个事务务须使用命令 BEGIN 或 START TRANSACTION，或者执行命令 SET AUTOCOMMIT=0，用来禁止使用当前会话的自动提交。



## 事务相关的命令

- **BEGIN** 或 **START TRANSACTION**；显式地开启一个事务；
- **COMMIT**；也可以使用**COMMIT WORK**，不过二者是等价的。**COMMIT**会提交事务，并使已对数据库进行的所有修改成为永久性的；
- **ROLLBACK**；有可以使用**ROLLBACK WORK**，不过二者是等价的。回滚会结束用户的事务，并撤销正在进行的所有未提交的修改；
- **SAVEPOINT identifier**；**SAVEPOINT**允许在事务中创建一个保存点，一个事务中可以有多个**SAVEPOINT**；
- **RELEASE SAVEPOINT identifier**；删除一个事务的保存点，当没有指定的保存点时，执行该语句会抛出一个异常；
- **ROLLBACK TO identifier**；把事务回滚到标记点；
- **SET TRANSACTION**；用来设置事务的隔离级别。InnoDB存储引擎提供事务的隔离级别有**READ UNCOMMITTED**、**READ COMMITTED**、**REPEATABLE READ**和**SERIALIZABLE**。



## 实际运行

```mysql
mysql> begin;
mysql> insert into table values(`abc`);
mysql> rollback;
```

begin 相当于启动一个新的事务，在同个 sock 上，之后所有的 sql 提交都会被认为是事务的一部分。

因为每次提交都是单独的，也就是每个请求都可能会失败。

如果遇到请求失败或者接到通知需要抛弃这些 commit 。就需要**ROLLBACK**。这样，之前提交的 commit 都会被撤回。

但是如果每个请求都完成了任务，则可以输入 **COMMIT** ，使之前提交的 commit 永久化。

要注意以下几点:

1. commit 提交必须是同个连接，如果使用线程池，需要注意同个事务必须使用同个连接。
2. 在 COMMIT 之前，数据都会被模拟的修改。也就是如果你 insert 一个数据，那通过 select 也可以查找出这条数据。




