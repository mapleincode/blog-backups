---
title: esxi 下 DSM 创建 ddsm IP 显示 169.254.x.x 问题解决
date: 2020-06-17 21:39:47
tags: [ esxi,Synology, Docker ]
---

群晖在  6.2.x 版本退出了 DDSM，即通过容器 docker 技术虚拟出一个 DSM 来以供特殊需求。

当然对我们来说，重要的是里面的序列号。



我在重装 esxi 后，尝试新建 DDSM ，却无一例外的发现 IP 地址显示 169.254.x.x 这个所谓本地保留地址。正常会出现在无法连接路由器 DHCP 服务情况下的电脑上面。



查阅了谷歌，在论坛上找到解决方案：

https://xpenology.com/forum/topic/12405-anyone-try-docker-ddsm-in-an-esxi-installation/

> Been there, done that and had the same result.
>
> The issue is more of a ESXi user issue, rather then an ESXi issue ![;)](https://raw.githubusercontent.com/mapleincode/images/master/img/20200617214244.gif)
>
>  
>
> Make sure your vSwtich has following settings in the security policy it uses:
>
> Allow promiscuous mode -> Yes
>
> Allow forged transmits -> Yes
>
> Allow MAC changes -> Yes

大致意思是说和 vSwitch 上的配置有关。需要把 

- 混合模式
- MAC 地址更改
- 伪传输

都必须是拒绝。

我去查看了 vswtich 的设置，发现`允许混合模式`选择的是拒绝。

更改设置，重新启动 DDSM ，就可以发现被分配了正常的 IP 地址。