---
title: "Centos7 防火墙 firewalld 基本配置与端口转发"
date: 2022-04-19 16:34:06
tags: ['centos', 'linux', 'firewalld']
---



# Centos7 防火墙 firewalld 基本配置与端口转发

[转载自 https://cloud.tencent.com/developer/article/1404032](https://cloud.tencent.com/developer/article/1404032)



# 1. firewalld 基本介绍

​    Centos7 开始已经放弃 iptables，转而使用 firewalld。从本质意义上讲，iptables 和 firewalld 是防火墙软件，其实现方式都是调用内核 Netfilter。firewalld 提供了一个动态管理的防火墙，形成网络 “zones” 规则集，具备支持 ipv4 和 ipv6 的能力。firewalld程序提供了图形化的配置工具 firewall-confighe、system-config-firewall 和命令行 firewall-cmd，用于配置 firewalld 永久性或非永久性规则。

# 2. firewalld 服务配置

启动服务：systemctl start firewalld

关闭服务：systemctl stop firewalld

重启服务：systemctl restart firewalld

查看服务状态：systemctl status firewalld

开机自启服务：systemctl enable firewalld

开机禁用服务：systemctl disable firewalld

查看是否开机自启：systemctl is-enable firewalld

>  PS：systemctl常见其他命令：

查看已启动的服务列表：systemctl list-unit-files | grep enabled

查看启动失败的服务列表：systemctl --failed

# 3. firewalld 规则配置

查看版本：firewall-cmd --version

查看帮助：firewall-cmd --help

查看状态：firewall-cmd --state

查看所有打开的端口：firewalld-cmd --zone=public --list-ports

查看所有规则：firewall-cmd --list-all

重载规则：firewall-cmd --reload

查看区域信息：firewall-cmd --get-active-zones

查看指定接口所属区域： firewall-cmd --get-zone-of-interface=enp4s0

拒绝所有包：firewall-cmd --panic-on

取消拒绝所有包： firewall-cmd --panic-off

查看是否拒绝： firewall-cmd --query-panic

# 4. firewalld端口规则

添加端口：firewall-cmd --add-port=80/tcp --permanent

移除端口：firewall-cmd --remove-port=80/tcp --permanent

查看端口状态：firewall-cmd --zone=public --query-port=80/tcp

（PS：添加或删除端口后不会立即生效，配置完 --reload 后才能生效；--permanent 代表永久生效）



# 5. firewalld端口转发

开启防火墙伪装：firewall-cmd --add-masquerade --permanent  //开启后才能转发端口

添加转发规则：firewall-cmd --add-forward-port=port=80:proto=tcp:toport=8080:toaddr=192.168.1.1 --permanent

>  PS：此规则将本机80端口转发到 192.168.1.1 的 8080 端口上，配置完 --reload 才生效

如果配置完以上规则后仍不生效，检查防火墙是否开启 80 端口，如果 80 端口已开启，仍无法转发，可能是由于内核参数文件 sysctl.conf 未配置ip转发功能，具体配置如下：

```
vi /etc/sysctl.conf
```

在文本内容中添加：`net.ipv4.ip_forward = 1`

保存文件后，输入命令 sysctl -p 生效



> 也可以直接修改配置文件
>
> ```ruby
> echo 1 >/proc/sys/net/ipv4/ip_forward
> ```