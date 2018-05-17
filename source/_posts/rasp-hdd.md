---
title: 树莓派用机械硬盘当系统盘
date: 2018-01-22 13:52:28
tags: 树莓派
---
>   全程参考一个大佬的文章 [https://mynook.info/blog/post/boot-raspberrypi-from-external-hdd]()

## 方案

一直对树莓派的读写速度十分不能接受，也曾想去某宝上找 EMMC 转 SD 的套件，但是听那个老板说 EMMC 芯片一百+，觉得有点贵，毕竟树莓派也仅仅支持 USB2.0。于是乎，在某家库存店找了一个 80G 的库存的机械硬盘(￥60)，又买了一根双 USB 的易驱线(树莓派的 USB 供电支持不了机械硬盘)。

>   简单搜索了一下发现直接从硬盘启动是不行的，因为树莓派启动的时候固定是从 SD 卡的第一个分区（/boot）读取 `config.txt`、`cmdline.txt` 和 `kernel.img` 这三个文件。但是除此之外，各个外接设备的挂载点则是没有限制的。于是我们可以将 `/boot` 分区放在 SD 卡上，并设置为只读的，避免意外关机时损坏上面的文件系统；同时我们把系统的其他部分都放在外置硬盘上，通过 USB 连接到树莓派，这部分是可读写的。

根据大佬的说法就是修改树莓派的启动文件，然后载入`/`分区时直接载入机械硬盘的那部分。

默认树莓派刷系统是直接将系统写入到 sd 卡然后再启动，尤其是默认的 `Raspbian`系统(我这边用的是`Ubuntu Desktop`，写入系统后还需要重新安装)。然后启动就可以进入系统。默认系统会分为两个区:

1.  `/dev/mmcblk0p1` 启动文件目录
2.  `/dev/mmcblk0p2` 实际系统目录

## 复制分区

首先将 `/dev/mmcblk0p2` 分区内的内容写入到机械硬盘。

1.  查询机械硬盘所在的分区

    ```sh
    sudo fdisk -l
    ```

2.  写入文件

    ```sh
    dd if=/dev/mmcblk0p2 of=/dev/sda1
    ```

其实无非是将  SD 卡的数据`dd`写入到机械硬盘，所以并不一定需要树莓派写。我是在`windows`环境上写入，因为支持 USB3.0 可以更快点。

## 修改启动

修改需要两个地方

先是修改`/boot/cmdline.txt`，将其中 `root=/dev/mmcblk0p2` 改为 `root=PARTUUID=分区的UUID` 然后保存。

机械硬盘分区的`UUID`通过

```sh
sudo gdisk /dev/sda
```

获得。

然后修改`/etc/fstab`。

修改`/boot`分区为只读，以免 SD 卡耗损。并且从机械盘上挂载`/`。

默认的内容为:

```sh
proc            /proc           proc    defaults                   0       0
/dev/mmcblk0p1  /boot           vfat    defaults                   0       2
/dev/mmcblk0p2  /               ext4    defaults,noatime           0       1
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
```

修改后的内容为:

```sh
proc            /proc           proc    defaults                    0       0
/dev/mmcblk0p1  /boot           vfat    ro                          0       2
/dev/sda1       /               ext4    defaults,errors=remount-ro  0       1
#/dev/mmcblk0p2  /               ext4    defaults,noatime           0       1
# a swapfile is not a swap partition, no line here
#   use  dphys-swapfile swap[on|off]  for that
```

## 如果失败

>   在过程中如果不慎修改错了参数，或者将不正确的分区挂载为只读了，不必惊慌，也不需要马上重装系统。只要将树莓派断电，SD 卡拔出来连接到别的电脑上，修改 `/boot/cmdline.txt` 里的参数，改为默认的挂载 SD 卡第二分区即可。重启后可以通过 `sudo mount -o remount,rw /dev/sda1` 将 `/etc/fstab` 中指定的分区重新挂载为可读写模式来进行修改。



## 最后

重启之后树莓派正常启动。用了一段时间发现若干问题。



1.  噪音，我丢在书房，影响不大，但是对声音有要求的同学可能要注意下。
2.  发热，虽然冬天，那是机械盘的温度至少有 30 ℃ +，不知道夏天会不会有这样的问题。因为我的机械盘是 80G 库存，可能是七八年前的产物，所以技术上也会有影响，如果采用新产的机械硬盘，其发热是否会好点么。

有几点以后还能继续尝试下:

1.  因为我的机械盘是老盘，性能底下，读写速度都不高(略后悔)。理论上 USB 2.0 虽然极限是 30MB/s，但是应该不会影响 4K 性能，所以换一个新硬盘是否可以再次提高速度 ？
2.  某宝上有卖带银灿主控的 U 盘，理论上这种 U 盘应该有不错的性能。(后面搜索了相关，据说只有 `SLC`存储芯片的性能会相对比较好，估计是因为功率问题，只能采用功率比较低的主控和芯片，而`SLC`的芯片 16G U 盘在某宝是 100 元左右)
3.  换 SSD 如何 ？
