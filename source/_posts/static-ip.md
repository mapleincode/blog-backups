---
title: Linux 静态 IP 设置
date: 2018-03-28 18:00:35
tags: [ Linux, Ubuntu ]
---
整理虚拟机的 IP。

路由器原来的 IP 段是`192.168.2.0`。为了方便管理，想把所有主机的 IP 改成 `192.168.1.x`。



## 子网掩码

虽然大学教过但是完全不知道这是什么东西。

抽空看了一个小时基本理解了他的作用。

`将`子网掩码从`255.255.255.0`改成`255.255.252.0`，这样就可以接受 `192.168.0.x -> 192.168.3.x`的 IP 了。



## 配置静态 IP

Ubuntu 下配置 IP 的文件是`/etc/network/interfaces`。

默认配置为:

```
# The primary network interface
auto ens32
iface ens32 inet dhcp
```

其中 `ens32`是你的网卡名称，可以通过`ifconfig`查看。默认模式为`dhcp`。

修改为:

```
iface ens32 inet static # 改为 static
address 192.168.1.101   # IP
netmask 255.255.252.0   # 子网掩码
gateway 192.168.2.1     # 网关
```

然后重启网络服务(networking)

```sh
service networking restart
# systemctl restart networking
# reboot # emmmmmm
```



## DNS 修改

可能受到  Windows 的影响，一直把 DNS 认为和 IP 差不多的东西，实际上 DNS 可能更偏向属于服务的东西。

默认你可以使用网络提供商的、第三方的、或者自己缓存 dns 作为一个 dns 服务器（一般路由器好像都会默认缓存 DNS）。

Ubuntu 下 DNS 的配置文件为`/etc/resolv.conf`。因为 dns 不像 IP，一旦链接网络了就不方便更换。很多操作都可能会导致 DNS 变化（比如 OpenVPN）。所以这个文件被设定是每次重启电脑都重新生成。



要永久修改 DNS 有两个办法。

1.  修改 `/etc/network/interfaces`，在配置的网卡信息后加一句

    ```
    dns-nameservers 8.8.8.8
    ```


2.  修改 `/etc/resolvconf/resolv.conf.d/base`，在里面添加

    ```
    nameserver 8.8.8.8
    ```



## 第三方 DNS 服务 

*   [阿里公共 DNS](http://www.alidns.com/)
    *   223.5.5.5
    *   223.6.6.6
*   [Google](https://developers.google.com/speed/public-dns/docs/using)
    *   8.8.8.8
    *   8.8.4.4
*   [114 DNS](http://www.114dns.com/)
    *   114.114.114.114
    *   114.114.115.115
