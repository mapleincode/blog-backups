---
title: raspberry 4B Ubuntu 系统 USB 启动
date: 2020-09-22 13:57:33
tags: [ raspberry ]
---



raspberry 4B，现在是觉得坑多于喜。

为了减少系统盘对性能的影响，所以我们使用外接 USB 系统盘的办法。

而树莓派默认只支持从 SD 读取。

> 隔壁 raspbian 已经支持无卡 usb 启动了，ubuntu 啥时候支持下？

早期是通过修改 cmdline.txt 里的 root

```
root=/dev/mmcblk0p2
```

修改为

```
root=/dev/sda2
```

也就是在 sda 上所在的系统分区。



而更新到 18.04.4 之后(大概)，cmdline.txt 改成了 btcmd.txt。而整个启动命令变成了

```
net.ifnames=0 dwc_otg.lpm_enable=0 console=tty1 root=LABEL=writable rootfstype=ext4 elevator=deadline rootwait
```

直接修改 root=/dev/sda2 好像不行（也可能操作步骤不对）。

然后查询了万能的 Google 答案。

https://askubuntu.com/questions/1237380/boot-ubuntu-20-04-on-a-raspberry-pi-4-from-ssd

> This means the root filesystem is identified by the partition label named 'writable'.

意思是目前系统盘启动是由标签管理的。

使用命令 

```
lsblk -o +LABEL
```

可以看到所有磁盘的标签

```
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT     LABEL
sda           8:0    0 670.7G  0 disk
├─sda1        8:1    0   256M  0 part /boot/firmware system-boot
└─sda2        8:2    0 670.5G  0 part /              writable
mmcblk0     179:0    0  29.7G  0 disk
├─mmcblk0p1 179:1    0   256M  0 part                system-boot
└─mmcblk0p2 179:2    0  29.5G  0 part                writable
```

可以看到 cmdline.txt 缩写的 LABEL=writable 指的是标签为 writable 的分区。而实际上标签为 writable 的分区有两个。

所以解决办法是，给 sd 卡上分区重命名。

```
sudo e2label /dev/mmcblk0p2 NEWNAME
```

重启之后，就能正常进入 USB 移动硬盘上的系统分区。