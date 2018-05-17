---
title: Shell 自动重启进程
date: 2018-02-11 16:55:24
tags: Shell
---

将 OpenVPN 搭建在一台服务器上，因为服务器设置了会话过期，所以服务每天都会在 24 小时后自动断开，因此需要一个类似守护进程的脚本来进行自动重启。



网上找到了一个简单的脚本:

```bash
#!/bin/bash

while true
do
    procnum=` ps -ef|grep "openvpn"|grep -v grep|wc -l`
   if [ $procnum -eq 0 ]; then
       ./openvpn
   fi
   sleep 30
done
```

其方式是每 30 秒通过 ps 命令来检查对应名称的进程是否存在，如果不存在，则重新启动进程。

因为我不需要日志的输出，因此对脚本稍微改造了下:

```bash
#!/bin/bash

while true
do
    procnum=` ps -ef|grep "openvpn"|grep -v grep|wc -l`
   if [ $procnum -eq 0 ]; then
       echo "OPENVPN: 重新启动 or 启动"
       ./openvpn > /dev/null
   fi
   echo "OPENVPN: 进程正在运行..."
   sleep 30
done
```

openvpn 会输出一大堆日志，而我不需要这些日志输出，也不用记录:

```bash
./run > /dev/null
```

## Then

启动脚本之后，发现并不是想预想的那样，脚本在每 30 s 进行检查进程，而是启动之后，一直处于停止状态。

主要原因是，我启动脚本的方式并非为后台启动，而是前台启动。而前台启动中会堵塞后面的行为，也就是不会继续检查进程。

所以，单纯对于这种前台运行的程序，其重启服务还能更加简单一点:

```Sh
while true
do
  echo "OPENVPN: 开始启动"
  ./openvpn > /dev/null
  echo "OPENVPN: 进程退出，正在重启..."
done
```

## End

这只是一种简单的守护进程，而并非是一个严谨的程序，只适合作为脚本来使用。

对于后台运行的程序，因为其可能因为程序错误而退出，而同时启动第一个脚本，可以保证在若干时间后自动重新启动脚本。



而对于前台启动的脚本，因为脚本启动运行本来就是同步的，因此，只要一个`while`循环就可以保证脚本在退出后自动重启。