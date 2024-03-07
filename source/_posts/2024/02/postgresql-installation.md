---
title: PostgreSQL Ubuntu 安装
date: 2024-03-07 19:47:37
tags: [postgresql]
---



## 安装流程

安装直接找官网，无脑走流程即可。

切记不要使用默认 apt 安装，默认 apt 仓库使用的是旧版本的 Postgres。

[官网地址安装教程](https://www.postgresql.org/download/linux/ubuntu/)



```bash
# Create the file repository configuration:
sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

# Import the repository signing key:
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

# Update the package lists:
sudo apt-get update

# Install the latest version of PostgreSQL.
# If you want a specific version, use 'postgresql-12' or similar instead of 'postgresql':
sudo apt-get -y install postgresql
```



这里要说明的一定是，默认安装就是最新版本，例如我目前安装的是 16.2 版本。

如果需要安装低版本，例如安装 12 版本。

```bash
apt-get install postgresql-12
```



> 实际上默认安装的就是 postgresql-16，所以你也可以直接 sudo apt-get -y install postgresql-16





## 修改配置

参考了 https://cn.linux-console.net/?p=22460



首先修改 `listen_addresse = '*'`，修改的文件为 `/etc/postgresql/16/main/postgresql.conf`。这个比较简单。



繁琐的是 pg_hba.conf，这个文件是用于链接数据库的校验选择。

先贴下最终配置:

```sh
# TYPE  DATABASE        USER            ADDRESS                 METHOD

local   all             postgres                                trust
local   all             all                                     scram-sha-256

host    all             all             0.0.0.0/0            scram-sha-256
host    all             all             ::1/128                 scram-sha-256

host 	all		all		0.0.0.0/0		md5
```

字段解释:

- local 通过 unix 本地访问
- host 通过 http 访问，分为 sslhost 和 nosslhost。
- DATABASE 选择 all，应该也支持根据数据库配置
- USER 可以选择 all 或者指定固定用户
- 地址指的是通过 host 访问网段。分为 ipv4 网络和 ipv6 网络。默认使用 ipv4，所以改成了 0.0.0.0/0
- METHOD 指的是鉴权方式：
  - trust 信任，不使用鉴权
  - peer 使用系统的鉴权，比如使用 postgres 账户的系统密码登录
  - md5 旧的验证模式
  - scram-sha-256 新的验证模式

权限是通过遍历的方式判断，所以再 host 同时配置了 md5 和 scram-sha-256 两种方式，以达到最好的兼容。

当然也可以都配置成 trust。



```shell
# PostgreSQL Client Authentication Configuration File
# ===================================================
#
# Refer to the "Client Authentication" section in the PostgreSQL
# documentation for a complete description of this file.  A short
# synopsis follows.
#
# ----------------------
# Authentication Records
# ----------------------
#
# This file controls: which hosts are allowed to connect, how clients
# are authenticated, which PostgreSQL user names they can use, which
# databases they can access.  Records take one of these forms:
#
# local         DATABASE  USER  METHOD  [OPTIONS]
# host          DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostssl       DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostnossl     DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostgssenc    DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostnogssenc  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
#
# (The uppercase items must be replaced by actual values.)
#
# The first field is the connection type:
# - "local" is a Unix-domain socket
# - "host" is a TCP/IP socket (encrypted or not)
# - "hostssl" is a TCP/IP socket that is SSL-encrypted
# - "hostnossl" is a TCP/IP socket that is not SSL-encrypted
# - "hostgssenc" is a TCP/IP socket that is GSSAPI-encrypted
# - "hostnogssenc" is a TCP/IP socket that is not GSSAPI-encrypted
#
# DATABASE can be "all", "sameuser", "samerole", "replication", a
# database name, a regular expression (if it starts with a slash (/))
# or a comma-separated list thereof.  The "all" keyword does not match
# "replication".  Access to replication must be enabled in a separate
# record (see example below).
#
# USER can be "all", a user name, a group name prefixed with "+", a
# regular expression (if it starts with a slash (/)) or a comma-separated
# list thereof.  In both the DATABASE and USER fields you can also write
# a file name prefixed with "@" to include names from a separate file.
#
# ADDRESS specifies the set of hosts the record matches.  It can be a
# host name, or it is made up of an IP address and a CIDR mask that is
# an integer (between 0 and 32 (IPv4) or 128 (IPv6) inclusive) that
# specifies the number of significant bits in the mask.  A host name
# that starts with a dot (.) matches a suffix of the actual host name.
# Alternatively, you can write an IP address and netmask in separate
# columns to specify the set of hosts.  Instead of a CIDR-address, you
# can write "samehost" to match any of the server's own IP addresses,
# or "samenet" to match any address in any subnet that the server is
# directly connected to.
#
# METHOD can be "trust", "reject", "md5", "password", "scram-sha-256",
# "gss", "sspi", "ident", "peer", "pam", "ldap", "radius" or "cert".
# Note that "password" sends passwords in clear text; "md5" or
# "scram-sha-256" are preferred since they send encrypted passwords.
#
# OPTIONS are a set of options for the authentication in the format
# NAME=VALUE.  The available options depend on the different
# authentication methods -- refer to the "Client Authentication"
# section in the documentation for a list of which options are
# available for which authentication methods.
#
# Database and user names containing spaces, commas, quotes and other
# special characters must be quoted.  Quoting one of the keywords
# "all", "sameuser", "samerole" or "replication" makes the name lose
# its special character, and just match a database or username with
# that name.
#
# ---------------
# Include Records
# ---------------
#
# This file allows the inclusion of external files or directories holding
# more records, using the following keywords:
#
# include           FILE
# include_if_exists FILE
# include_dir       DIRECTORY
#
# FILE is the file name to include, and DIR is the directory name containing
# the file(s) to include.  Any file in a directory will be loaded if suffixed
# with ".conf".  The files of a directory are ordered by name.
# include_if_exists ignores missing files.  FILE and DIRECTORY can be
# specified as a relative or an absolute path, and can be double-quoted if
# they contain spaces.
#
# -------------
# Miscellaneous
# -------------
#
# This file is read on server startup and when the server receives a
# SIGHUP signal.  If you edit the file on a running system, you have to
# SIGHUP the server for the changes to take effect, run "pg_ctl reload",
# or execute "SELECT pg_reload_conf()".
#
# ----------------------------------
# Put your actual configuration here
# ----------------------------------
#
# If you want to allow non-local connections, you need to add more
# "host" records.  In that case you will also need to make PostgreSQL
# listen on a non-local interface via the listen_addresses
# configuration parameter, or via the -i or -h command line switches.




# DO NOT DISABLE!
# If you change this first entry you will need to make sure that the
# database superuser can access the database using some other method.
# Noninteractive access to all databases is required during automatic
# maintenance (custom daily cronjobs, replication, and similar tasks).
#
# Database administrative login by Unix domain socket
local   all             postgres                                trust

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     scram-sha-256
# IPv4 local connections:
host    all             all             0.0.0.0/0            scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
# Allow replication connections from localhost, by a user with the
# replication privilege.
# local   replication     all                                     peer
# host    replication     all             127.0.0.1/32            scram-sha-256
# host    replication     all             ::1/128                 scram-sha-256
host 	all		all		0.0.0.0/0		md5
```



### 访问数据库

- sudo -u postgres psql 
- sudo -i -u postgres



### 设置 postgres 密码

```
ALTER USER postgres PASSWORD 'Str0ngP@ssw0rd';
```



测试密码是否生效:

```
$ psql -h localhost -U postgres
Password for user postgres: Enter_set_password
psql (16.0 (Ubuntu 16.0-1.pgdg20.04+1))
SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, compression: off)
Type "help" for help.

postgres=# 
```



### 创建用户

role 创建：

```sql
CREATE ROLE admin WITH LOGIN SUPERUSER CREATEDB CREATEROLE PASSWORD 'Passw0rd';

postgres=# \du
                             List of roles
 Role name |                         Attributes                         
-----------+------------------------------------------------------------
 admin     | Superuser, Create role, Create DB
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS
```



用户 user 创建:

```sql
CREATE USER demo_user with encrypted password 'PassW0rd';
GRANT ALL PRIVILEGES ON DATABASE sampleDB to demo_user;
```



两者的区别：

> CREATE USER is now an alias for CREATE ROLE. The only difference is that when the command is spelled CREATE USER, LOGIN is assumed by default, whereas NOLOGIN is assumed when the command is spelled CREATE ROLE
>
> 原文链接：https://blog.csdn.net/qq_35462323/article/details/102695597



## 完全卸载

参考 https://blog.csdn.net/qq_26656329/article/details/78562310

我一开始同时安装了 14 和 16 版本。导致启动出了问题。

- 删除相关的安装

```
sudo apt-get --purge remove postgresql\*1
```



- 删除配置及文相关件

```
sudo rm -r /etc/postgresql/
sudo rm -r /etc/postgresql-common/
sudo rm -r /var/lib/postgresql/123
```



- 删除用户和所在组

```
sudo userdel -r postgres
sudo groupdel postgres12
```

