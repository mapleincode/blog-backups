---
title: 编译 OpenVPN 及解决相关依赖问题
date: 2018-05-17 10:31:18
tags: [ OpenVPN ]
---



> **OpenVPN** 是全功能的 SSL VPN，它使用行业标准 SSL/TLS 协议实现 OSI 第 2 层或第 3 层安全网络扩展，支持基于证书，智能卡和/或用户名/密码凭据的灵活的客户端认证方法，并允许用户或使用防火墙规则应用于 VPN 虚拟接口的组专用访问控制策略。OpenVPN 不是 Web 应用程序代理，不通过 Web 浏览器进行操作。

在 [OpenVpn](https://openvpn.net/index.php/open-source/downloads.html) 官网下载最新版本的源码包，解压后按开始编译

```
$ ./configure
$ make
$ sudo make install
```

configure 过程中会出现相关依赖未安装的错误提示

- **configure: error: openssl check failed**
- **configure: error: ssl is required but missing**

该错误提示缺少 libssl-dev

解决方法

```
$ sudo apt install libssl-dev
```

- **configure: error: route utility is required but missing**
  该错误提示缺少 net-tools

> **route utility**
> 每个主机和每个路由器都包含自己的路由表。这些包含分配 IP 地址的路由。 Route命令用于在路由表中手动添加，删除和编辑条目。

解决方法

```
$ sudo apt install net-tools
```

- **configure: error: lzo enabled but missing**
  该错误提示缺少 liblzo2-dev

> **lzo**
> LZO 是致力于解压速度的一种数据压缩算法，LZO 是 Lempel-Ziv-Oberhumer 的缩写。这个算法是无损算法，参考实现程序是线程安全的。

解决方法

```sh
$ sudo apt-get install liblzo2-dev
```

- **configure: error: libpam required but missing**
  该错误提示缺少 libpam0g-dev

> **pam**
> PAM 可以使程序开发与认证方式细节分离，而是在程序运行时调用“认证”模型完成工作。认证模型可以由本地系统管理员通过配置进行选择

解决方法

```sh
$ sudo apt-get install libpam0g-dev
```

原文地址：[编译 OpenVPN 及解决相关依赖问题](https://chinesejar.github.io/2017/05/14/HowToMakeOpenVPN/)
