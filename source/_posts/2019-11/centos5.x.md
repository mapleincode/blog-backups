---
title: CentOS 5.x 版本 repo 地址
date: 2019-11-13 16:32:214
tags: [ centos ]
---



在迁移虚拟机到 ESXi 的时候，不幸遗漏了一个游戏私服服务器 orz。虽然私服不怎么好玩，但是也算是一个小纪念。鉴于私服的服务器版本是 CentOS 5.x 版本比较老，所以担心之后可能搭不起来。

于是乎，果然访问 error。查阅了相关信息，我搭建的时候 16 年年底。17 年 3 月，CentOS 5.x 正式停止依赖维护。

呃...查询了网上资料，有提供一个 `http://vault.centos.org/5.11/os` 的 repo 地址。并且版本限定 5.11。鉴于我对 CentOS 不太了解，直接拿了网上的 CentOS-Base.repo。



```bash
[base]
name=CentOS-5.11 - Base
failovermethod=priority
baseurl=http://vault.centos.org/5.11/os/$basearch/
gpgcheck=1
gpgkey=http://vault.centos.org/RPM-GPG-KEY-CentOS-5

#released updates
[updates]
name=CentOS-5.11 - Updates
failovermethod=priority
baseurl=http://vault.centos.org/5.11/updates/$basearch/
gpgcheck=1
gpgkey=http://vault.centos.org/RPM-GPG-KEY-CentOS-5

#additional packages that may be useful
[extras]
name=CentOS-5.11 - Extras
failovermethod=priority
baseurl=http://vault.centos.org/5.11/extras/$basearch/
gpgcheck=1
gpgkey=http://vault.centos.org/RPM-GPG-KEY-CentOS-5

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-5.11 - Plus
failovermethod=priority
baseurl=http://vault.centos.org/5.11/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://vault.centos.org/RPM-GPG-KEY-CentOS-5

#contrib - packages by Centos Users
[contrib]
name=CentOS-5.11 - Contrib
failovermethod=priority
baseurl=http://vault.centos.org/5.11/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://vault.centos.org/RPM-GPG-KEY-CentOS-5
```



原文: https://www.javatang.com/archives/2017/09/04/13261799.html