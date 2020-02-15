<!--
 * @Author: maple
 * @Date: 2020-01-18 01:47:22
 * @LastEditors: maple
 * @LastEditTime: 2020-02-15 17:27:39
 -->
---
date: 2020-01-18 01:48:00
title: nameserver 127.0.0.53
tags: Ubuntu
---

Ubuntu 升级到 16.04 还是 18.04 的时候，默认会启动 NetWorkManage 的一个功能，即把 `/etc/resolv.conf` 的 dns 地址配置成

````
nameserver 127.0.0.53
````

正常情况下不会有影响，但是如果想要关闭这个功能，设置

```
$ sudo systemctl disable systemd-resolved
$ sudo reboot
```

> 建议正常运作的情况下不需要卸载这个组件



参考: https://www.hiroom2.com/2017/08/24/ubuntu-1610-nameserver-127-0-0-53-en/

