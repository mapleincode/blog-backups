---
title: "linux 端口转发 iptables"
date: 2022-04-19 16:33:29
tags: [liunx, iptables]
---



# linux 端口转发 iptables

**centos 7 使用 firewalld 服务而不是 iptables，如果 centos7 以上需要注意！！**

### 1.开启配置  iptables

**临时修改：**

```shell
$ echo 1 >/proc/sys/net/ipv4/ip_forward
```

默认值 0 是禁止 ip 转发，修改为 1 即开启 ip 转发功能。

**重启生效:**

修改 /etc/sysctl.conf 文件:

```
net.ipv4.ip_forward = 1
```

**其他:**

```shell
sysctl -w net.ipv4.ip_forward=1
```

> 查看路由表:
>
> netstat -rn 或 route -n
>
> 查看 iptables  规则:
>
> iptables -L
>
> 查看 iptbales nat 规则 :
>
> iptbales -t nat -L



### 2.简单转发

```shell
#-- 把访问本机 8091 端口的请求转发到 8090端口
iptables -t nat -A PREROUTING -p tcp --dport 8091 -j REDIRECT --to-ports 8090

# 通过 1.168 的 6666 端口访问 1.8 的 7777 端口
iptables -t nat -A PREROUTING -p tcp --dport 6666 -j DNAT --to-destination 192.168.1.8:7777
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.8 --dport 7777 -j SNAT --to-source 192.168.1.168

# 跨主机转发，除了把请求发给 7777 端口之外，还要保证 7777 的请求可以返回。
 
# centos 可以 save 和 restart
service iptables save
service iptables restart

# ubuntu 需要安装 iptable-persistence
sudo iptables-save > /etc/iptables/rules.v4
sudo ip6tables-save > /etc/iptables/rules.v6
# 删除直接删除文件
```



## 参考:

1. <https://www.jianshu.com/p/c35e7637e09b>

2. https://www.cnblogs.com/paul8339/p/14688156.html