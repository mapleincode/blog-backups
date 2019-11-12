---
title: 群晖 SFTP 连接失败
date: 2018-04-07 23:24:52
tags: [ 黑群辉 ]
---

群晖系统(Synology DiskStation)可以说是 NAS 系统中最完美的系统（虽然我也就用了他一个）。基本他集成了所有我们所需要的功能，而且性能强大。



因为需要在局域网内进行文件传输，因为平时都是用 SSH 来通讯的，所以直接用 NAS 用自带 SFTP 服务来传输文件非常方便。

之前有一次，将局域网里的某个机子重装了之后，在 NAS 上通过 SFTP 服务访问却得到了 `链接失败`。



考虑了下，应该是`known_hosts`捣的鬼。



常用 SSH 的人，对`known_hosts`一定很了解，本身它是为了安全而生，访问你访问到一个虚假的系统。通常，如果一个域名或者 IP 的 ssh-key 被更改之后（例如重装系统），都会提示对方的 hosts 和保存在文件里的信息不一致，而拒绝访问。通常在 bash 中的做法，是删除用户目录下`~/.ssh/known_host`中对应的那一行。



但是群晖系统并不是通过`root`用户来进行访问的(大雾)，所以他的 `known_hosts`文件并不是在用户目录下，所以记录下这个问题，以免到时候又要找半天。



首先开启 `控制面板/终端机/启用 SSH 功能`。



通过 SSH 访问 NAS。



这里有几点要注意下：

1. 用户普通账户是无法登陆的，也就是你平时自定义的用户名，默认情况下是无法登陆 SSH 的。
2. 控制面板中有一个 admin， 首先需要在`用户账户/用户账户/admin/编辑`，关闭禁用账户。虽然可以通过 admin 这个用户访问 SSH。但是他本身的权限并不是完全的。
3. 然后就是 root 账户，虽然 root 账户权限不是完全的 (super)。但是对于 admin 来说还是高了点。而且他也不需要在控制面板中进行设置，密码和 admin 密码是一致的，恐怕是一个隐藏的 BUG。



```sh
ssh root@192.168.0.1
```

通过 find 的函数来进行查找:

```
find / -name known_hosts |grep hosts
```

查到出结果，最奇怪的那个就是系统默认的 known_hosts。

```
./root/.ssh/known_hosts
./usr/syno/etc/synovfs/1026/sftp/known_hosts
./volume1/homes/admin/.ssh/known_hosts
```

修改那个 `known_hosts` 即可修复无法访问的 BUG。

> 我直接用 root 账户访问不能访问的服务器，然后保存到 root 用户目录下。后来发现 NAS 的 SFTP 服务居然也可以访问，可能是一个权限覆盖的逻辑吧。