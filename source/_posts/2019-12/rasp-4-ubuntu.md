---
date: 2019-12-24 07:56:03
title: Raspberry4 安装 Ubuntu
tags: [ raspberry, Ubuntu ]
---

> 随着自己年纪越来越大，事情越来越多，而相对的，记忆力却越来越差。
>
> 但是已经安装过一次 Raspberry4 ，但是不到几个月的时间就忘得一干二净，折腾了一整个晚上才完全搞定。
>
> 以此说明，以后应当认真记录一些操作的信息，以供事后参考。
>
> 真怀念自己当初的记忆力



Raspberry 4 代采用 USB3.0 和千兆网卡，无论是网速还是硬盘读取速度，相对于前几代有着本质的差异。我原先使用迅雷下载宝作为小型的 NAS 存储视频监控和正常查看的电视剧，但是下载宝作为低端的性能配置，IO 写入性能不理想，写入速度最高也仅有 30 MB/s，在对性能需求愈发高的情况下，我决定用 Raspberry4 代替下载宝。入手了一个 4G 版本的 Raspberry4。但是对于网关代理和简单服务依然放在 Raspberry2 上。

> Raspberry 4 支持 wifi、蓝牙、千兆网卡和 USB 3.0。而前者其实是 Raspberry3B + 也仅仅支持 USB3.0 和 300M 网卡。而且 Raspberry 4 使用了供电更稳定，接口更牢固的 Type-c 充电口。
>
> > type-c 充电口因为设计问题存在 BUG，使得不能使用可以某一类的数据线。我这边使用的是简单的手机充电线，就不会存在以上的问题。
>
> 值得说的一点是，Pi4 的千兆网口和 USB3.0 共享带宽。

> 原来的设定，Pi4 上用于 Samba 服务存储信息，并用于保存一些个人文件。而 Pi2 则用于作为反向代理的网关，直接在路由器上转发 http 和 ssh 端口。



这边想说我的 Pi2 是外接了一个硬盘，并且从硬盘上启动系统。由于 Pi2 是 USB2.0 ，所以理想速度也只有 20MB/s 左右。不过这基本符合我对网关的要求。然而，Pi2 默认只支持 2A 的电源，加上其 microUSB 的设计，使得稍微移动下设备都会导致硬盘断开链接。更别说电压不稳定导致的断开。在多次经历宕机之后，我决定把 Pi2 也更换成 Pi4。

在咸鱼上找到了一个大学科研狗，说是全新为查封，1G 版本卖 220。价格很诱人。然而收到货的时候，包装完全是拆分状态，好在成色很新，就当做是快递暴力把包装摔开了= - =。

我的第一个 Pi4 入手比较早，Ubuntu 那时候还没有出支持 Pi4，我参考了国外大佬的一个操作。但是不久后就看到官方提示支持，然后今天上去一看，发现支持的是 19.10 版本。呃….不是长期支持版，那我还是继续折腾 18.04。



最后折腾了整整一个晚上 + 掰断了一张 SD 卡 + 搞坏了一个硬盘盒。

泪目( Ĭ ^ Ĭ )。

以下是操作流程，以此记录。

> 整个流程参考:
>
> https://jamesachambers.com/raspberry-pi-ubuntu-server-18-04-2-installation-guide/

## 需要准备

必须

1. Pi4
2. TYPE-C 线
3. SD 卡
4. SD 读写器



非必须

1. micro HDMI 转 HDMI 接口
2. HDMI 线
3. 显示器
4. 移动硬盘 or 易驱



> Ubuntu 安装支持自动安装，正常流程之后会开启 SSH 服务。所以即使没有显示器，也可以正常安装。当然接了显示器就可以更加清晰的看到启动状况。
>
> Pi4 加强了供电，加上支持 USB3.0，如果是作为 mini 服务器强烈建议把系统写在 SSD 中。



## 下载镜像

**64 位 (aarch64)** 

这位大佬直接制作了 64 位的镜像可以直接使用。

> 支持了 4GB RAM 的寻址，也修复了 WiFI。

下载地址: https://github.com/TheRemote/Ubuntu-Server-raspi4-unofficial

**32 位(armhf)**

> 由于官方并没有提供 18.04 的系统镜像，所以 32 位是基于 Pi3 的镜像进行修改。

http://cdimage.ubuntu.com/releases/bionic/release/ubuntu-18.04.3-preinstalled-server-armhf+raspi3.img.xz



## 写入镜像 & 修改镜像

写入方式:

```
xzcat ~/Downloads/ev3dev-yyyy-mm-dd.img.xz | sudo dd bs=4M of=/dev/sdb
```



修改镜像:

> 64 位镜像已经修改，可以直接使用

首先是创建一个文件夹，然后把 SD 卡挂载到启动区

```sh
mkdir raspboot
mount /dev/sdb2 raspboot
```

然后删除所有里面的文件

```
cd raspboot
rm -rf *
```



下载官方 Github 库中的固件

[//github.com/raspberrypi/firmware/archive/master.zip](https://codeload.github.com/raspberrypi/firmware/zip/master)

解压之后把文件夹里所有的文件都 copy 到 raspboot 文件夹 (也就是 sd 卡的启动区)。



然后在启动区创建 cmdline.txt 和 config.txt



cmdline.txt

```
dwc_otg.fiq_fix_enable=2 console=ttyAMA0,115200 kgdboc=ttyAMA0,115200 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 rootwait rootflags=noload net.ifnames=0
```



config.txt

```
# Enable audio (loads snd_bcm2835)
dtparam=audio=on

[pi4]

[all]
```

## 启动



然后插入到 Pi4 并启动。

第一次启动会比较慢，需要等待几分钟。启动之后就会自动开启 SSH。默认用户密码为

```
ubuntu / ubuntu
```



## 修复 apt-get update 错误

由于 18.04 官方并未支持 Pi4 。所以在 apt 源会识别成错误的固件。作者给的办法是，关闭 flash-kernel 内核的更新:

```sh
sudo apt-mark hold flash-kernel
```

这样，只要不使用 `dist-upgrade `，正常使用都不会有问题。

一旦官方支持了之后，重新 `unhold` 之后就可以继续正常更新。



## USB 启动

> 即把系统安装在 USB 外接设备上。作者这边直接用 SSD，奢侈~~

首先是把用同样的办法写入移动硬盘。然后修改 sd 卡上的 cmdline.txt 文件。把 root=/dev/mmcblk0p2 改成 root=/dev/sda2 。然后把移动硬盘插入到 Pi4 的 USB3.0 接口上，正常启动。

> 为了保证设备稳定性，请使用 3A 以上的电源。

这里要注意一点是，如果正常写入的话，硬盘只会有 3G 左右的分区，而其他分区都是未创建状态。理论上可以在树莓派里直接操作。但是因为我是在 Linux 写入系统的，直接打开 GParted 然后把分区改成最大，然后保存就 OK 了。

如果连接移动硬盘后，系统依然进了 SD 卡的分区 (可以通过 df -h 查看分区容量来确认)。可以直接修改 /boot/firmware 中的 cmdline.txt （相当于启动分区），然后重启即可。



## Wifi 修复

作者提示说 wifi 会因为系统版本问题(Pi3 版本)，导致系统卡主一段时间。

```sh
sudo sed -i "s:0x48200100:0x44200100:g" /lib/firmware/brcm/brcmfmac43455-sdio.txt
```

然后重启系统。



## 修改用户名 & 用户组 & 用户路径 & 系统名称

首先是登录 root 用户(不能使用 SSH)。

然后

```sh
# 修改用户名
sudo usermod -l new_username ubuntu

# 修改用户目录
sudo mv ubuntu xxxx
sudo usermod -d /home/xxxx -m xxxx

# 修改用户组
sudo groupmod -n xxxx ubuntu
```



因为这个办法需要借助显示器，或者新建一个支持 ssh 的用户，如果觉得麻烦可以修改:

- /etc/passwd
- /etc/shadow
- /etc/group

这三个文件。来实现重命名的效果。



还有一点是修改系统名称，直接修改 /etc/hostname 即可。



## 修改时区

```sh
sudo dpkg-reconfigure tzdata
```



## 修改 GPU 内存占用

默认 1GB 版本会使用 76MB 内存作为 GPU 显存。对于服务器来说，显存的量其实并没有那么重要。作者建议把显存修改成 16MB。

修改 `/boot/firmware/config.txt` 文件，加入

```
gpu_mem=16
```

然后重启即可。



