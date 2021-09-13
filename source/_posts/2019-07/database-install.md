---
title: 常用数据库(缓存)的简单安装配置
date: 2017-11-21 17:35:52
tags: [ mysql, mongodb, redis, memcache]
---

用于日常代码调试测试开发。
如果稳定使用建议 docker 搭建数据库。

## 环境

树莓派 Ubuntu 16.04

## MySQL

### 安装
```bash
sudo apt install mysql-server
```

### 配置

MySQL 的配置文件位于 `/etc/mysql/`。

一般名称为`my.cnf`或者`mysqld.cnf`。

因为一般开发用的 MySQL 都是外网调用，所以需要修改`bind-address`。注释或者修改成你所在的局域网的 IP。(如果数据库在公网暴露且有信息存储，请勿注释该代码)。

### 创建用户并授权

#### 创建用户

```sql
CREATE USER "hello"@"%" IDENTIFIED BY "password";
```

* `hello`: 用户名
* `%`: 访问的 IP 或者 IP 段。`%`表示不限制。
* `password`: 登录密码。 

> 新版本 mysql 创建用户之后，用 GUI 登录会返回密码错误。
>
> 需要创建时加入 `WITH mysql_native_password` 参数
>
> ```
> CREATE USER "hello"@"%" IDENTIFIED WITH mysql_native_password BY 'yourpassword';
> ```
>
> 

#### 授权用户

```sql
GRANT SELECT,UPDATE ON 'DATABASE'.'TABLE' TO 'user'@'%';
```

* SELECT: 授权权限
* DATABASE: 数据库。全部则写 *。
* TABLE: 表。全部写 *。
* user: user。
* `%`: 访问的 IP 或者 IP 段。`%`表示不限制。

如果只是用于开发，最简单就是

```sql
GRANT ALL ON *.* to 'USER'@'%';
```


#### MySQL中的操作权限
```sql
| `ALTER`                  | Allows use of `ALTER TABLE`.             |
| ------------------------ | ---------------------------------------- |
| `ALTER ROUTINE`          | Alters or drops stored routines.         |
| `CREATE`                 | Allows use of `CREATE TABLE`.            |
| `CREATE ROUTINE`         | Creates stored routines.                 |
| `CREATE TEMPORARY TABLE` | Allows use of `CREATE TEMPORARY TABLE`.  |
| `CREATE USER`            | Allows use of `CREATE USER`, `DROP USER`, `RENAME USER`, and `REVOKE ALL PRIVILEGES`. |
| `CREATE VIEW`            | Allows use of `CREATE VIEW`.             |
| `DELETE`                 | Allows use of `DELETE`.                  |
| `DROP`                   | Allows use of `DROP TABLE`.              |
| `EXECUTE`                | Allows the user to run stored routines.  |
| `FILE`                   | Allows use of `SELECT`... `INTO OUTFILE` and `LOAD DATA INFILE`. |
| `INDEX`                  | Allows use of `CREATE INDEX` and `DROP INDEX`. |
| `INSERT`                 | Allows use of `INSERT`.                  |
| `LOCK TABLES`            | Allows use of `LOCK TABLES` on tables for which the user also has `SELECT` privileges. |
| `PROCESS`                | Allows use of `SHOW FULL PROCESSLIST`.   |
| `RELOAD`                 | Allows use of `FLUSH`.                   |
| `REPLICATION`            | Allows the user to ask where slave or master |
| `CLIENT`                 | servers are.                             |
| `REPLICATION SLAVE`      | Needed for replication slaves.           |
| `SELECT`                 | Allows use of `SELECT`.                  |
| `SHOW DATABASES`         | Allows use of `SHOW DATABASES`.          |
| `SHOW VIEW`              | Allows use of `SHOW CREATE VIEW`.        |
| `SHUTDOWN`               | Allows use of `mysqladmin shutdown`.     |
| `SUPER`                  | Allows use of `CHANGE MASTER`, `KILL`, `PURGE MASTER LOGS`, and `SET GLOBAL` SQL statements. Allows `mysqladmin debug` command. Allows one extra connection to be made if maximum connections are reached. |
| `UPDATE`                 | Allows use of `UPDATE`.                  |
| `USAGE`                  | Allows connection without any specific privileges. |
```

## MongoDB

### 安装

```bash
sudo apt install mongodb
```

### 配置

Mongo 的配置文件一般为 `/etc/mongodb.conf`。

#### bind-ip
要修改的点还是 `bind_ip`。

修改为：

```bash
bind_ip = 0.0.0.0
```

通过注释`bind_ip`并不能生效。

#### 开启权限

其实是权限问题。Mongo 默认是不开启权限。但是 Mongo 不像 MySQL 安装时会配置一个 root 用户。

所以只能先配置好登录用户再开启权限 (auth)。

在本机上登录 mongodb，
输入: 
```javascript
use admin; // 切换数据库

db.createUser({
    user: "userName",
    pwd: "password"
    roles: [
        "root"
    ]
});
// 创建用户成功
```

官方给的`createUser`文档:

```javascript
{ user: "<name>",
  pwd: "<cleartext password>",
  customData: { <any information> },
  roles: [
    { role: "<role>", db: "<database>" } | "<role>",
    ...
  ]
}
```

其实还有个快捷的方法(应该说是准备被淘汰的方法):

```javascript
db.addUser('username', 'password');
```

创建完用户之后，可以修改`mongodb.conf` `auth yes`开启权限。

#### 命令行鉴权

在`mongo`命令行的方式为:
```javascript
use admin;
db.auth('username', 'password');
```

## Redis


### 安装
```shell
sudo apt install redis-server
```

### 配置

redis 的配置文件一般为`/etc/redis/redis.conf`。

#### bind-ip

注释 conf 中的 `# bind 127.0.0.1`。

> 新版本需要配置`protected-mode no`,否则会无法访问

#### 开启权限

redis 并没有用户系统。但是可以设置口令登录。

修改 conf 中的`requirepass`。
```shell
···
requirepass password
···
```
重启 redis 服务即可。


## Memcache

### 安装

```shell
sudo apt install memcached
```

### 配置

memcached 默认没有启动，需要手动启动。

直接贴上搜到的阿里云**Memcached服务安全加固方案**



#### Memcached服务加固方案

1.  配置访问控制。
    建议用户不要将服务发布到互联网上而被黑客利用，可以通过[ECS安全组规则](https://help.aliyun.com/document_detail/25475.html.html)或IPtables配置访问控制规则。
    例如，在Linux环境中运行命令`iptables -A INPUT -p tcp -s 192.168.0.2 —dport 11211 -j ACCEPT`，在IPtables中添加此规则只允许192.168.0.2这个IP对11211端口进行访问。

2.  绑定监听IP。
    如果Memcached没有在公网开放的必要，可在Memcached启动时指定绑定的IP地址为 127.0.0.1。例如，在Linux环境中运行以下命令：
    `memcached -d -m 1024 -u memcached -l 127.0.0.1 -p 11211 -c 1024 -P /tmp/memcached.pid`

3.  最小化权限运行。
    使用普通权限账号运行，指定Memcached用户。例如，在Linux环境中运行以下命令来运行Memcached：
    `memcached -d -m 1024 -u memcached -l 127.0.0.1 -p 11211 -c 1024 -P /tmp/memcached.pid`

4.  修改默认端口。
    修改默认11211监听端口为11222端口。在Linux环境中运行以下命令：
    `memcached -d -m 1024 -u memcached -l 127.0.0.1 -p 11222 -c 1024 -P /tmp/memcached.pid`

    **Memcached命令参数说明**

    -   -d 是指启动一个守护进程。
    -   -m 是指分配给Memcached使用的内存数量，单位是MB，以上为1024MB。
    -   -u 是指运行Memcached的用户，推荐使用单独普通权限用户memcached，而不要使用root权限账户。
    -   -l 是指监听的服务器IP地址，例如指定服务器的IP地址为127.0.0.1。
    -   -p 是用来设置Memcached的监听端口，默认端口为11211。建议设置1024以上的端口。
    -   -c 是指最大运行的并发连接数，默认是1024。可按照您服务器的负载量来设定。
    -   -P 是指设置保存Memcached的pid文件，例如保存在 /tmp/memcached.pid 位置。

5.  备份数据。
    为避免数丢失，升级前请做好备份，或者建立ECS硬盘快照。


