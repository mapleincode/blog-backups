---
title: "树莓派 4B 安装 watchdog 以解决崩溃无响应问题"
date: 2021-09-13 14:32:12
tags: [ raspberry ]
---

树莓派 4B 尽管作为服务器来说，性能相当捉急。但是作为保护服务器安全的第一道安全防线，却有着天然的优势。

在高强度的攻击下，树莓派会直接崩溃挂掉，从而保护通过其作为代理而被保护的服务。

然而，并不会有人来攻击我的服务，而是我经常因为树莓派意外挂掉，导致无法正常使用我的服务。

解决方案是安装 watchdog。在系统崩溃时，自动重启服务。



> 系统:
>
> - raspberry 4B
> - ubuntu 20.04



## 安装流程

1. 在 config.txt 从硬件上打开 watchdog 功能

   ```shell
   sudo echo 'dtparam=watchdog=on' >> /boot/config.txt
   sudo reboot
   ```

   

2. 安装 watchdog 依赖

   ```shell
   sudo apt-get update
   sudo apt-get install watchdog
   ```

   

3. 配置 watchdog

   ```shell
   sudo echo 'watchdog-device = /dev/watchdog' >> /etc/watchdog.conf
   sudo echo 'watchdog-timeout = 15' >> /etc/watchdog.conf
   sudo echo 'max-load-1 = 24' >> /etc/watchdog.conf
   ```

   

4. 启动服务

   ```shell
   sudo systemctl enable watchdog   # 设置自动启动
   sudo systemctl start watchdog    # 启动
   sudo systemctl status watchdog   # 查看状态
   ```



## 测试功能

输入报错指令

```shell
sudo bash -c ':(){ :|:& };:'
```



如果树莓派崩溃后自动重启，说明功能正常！



## 参考

- https://diode.io/raspberry%20pi/running-forever-with-the-raspberry-pi-hardware-watchdog-20202/

