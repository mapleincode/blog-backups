---
title: VLAN
date: 2022-06-07 10:16:21
tags: VLAN
---



ACCESS 端口接 PC，VID = PVID
TRUNK 端口级联，VID = 全部，PVID = 1



**VID = VLAN ID**

**PVID = Port-Base VLAN ID**



VID（VLAN ID）是VLAN的标识，定义其中的端口可以接收发自这个VLAN的包

> 当端口1同时属于 VLAN1、VLAN2 和 VLAN3 时, 那么端口1可以接收到 VLAN1，2，3的数据



PVID（Port VLAN ID）定义这个 untag 端口可以转发哪个 VLAN 的包

> 而它的PVID为1,发出的包只能发到 VLAN1 中



PVID 并不是加在帧头的标记，而是端口的属性，用来标识端口接收到的未标记的帧。

也就是说，当端口收到一个未标记的帧时，则把该帧转发到 VID 和本端口 PVID 相等的 VLAN 中去。

