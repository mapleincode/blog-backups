---
title: 黑群晖的安装 & 洗白
date: 2019-03-29 11:08:23
tags: [黑群晖]
---

> **本帖只作为技术研究， 请读者遵守相关法律法规，不要侵犯以他人的商业利益为目的进行相关破解行为。**

## 准备工作

硬件:

- 一台可以上网并带有 USB 口的 windows 电脑
- 一个 U 盘（作为启动盘而非安装盘，需要长期插在机子上）
- nas（主机）

软件: 

- ChipEasy
- Win32DiskImager
- DiskGenius

下载地址:

[<https://pan.baidu.com/s/1BVbFroBwtCCdNm2xCuaCHg#list/path=%2F>](https://pan.baidu.com/s/1BVbFroBwtCCdNm2xCuaCHg#list/path=%2F)

## 安装群晖系统

1. 首先获取 nas 的 mac 并记录。获取办法建议通过路由器。
2. 获取 U 盘的 vid 和 cid 。通过 ChipEasy 获取。linux 上可以通过 `lsusb` 指令获取。
3. 用 Win32DiskImager 写入 img 启动镜像到 U 盘。
4. 打开 DiskGenius 找到 U盘目录下 `/grup/grup.cfg` (大致目录记不清了)。右键复制到桌面
5. 用文本编辑软件打开，修改 vid 、cid 、mac 地址。(搜索下 mac 就能定位地址)。
6. 将 cfg 文件覆盖回 U 盘。
7. 在 nas 上用 u 盘启动。
8. 然后按照下一步下载 & 安装系统即可。

##  PhotoStation 支持视频缩略图

> 此办法不算洗白，只是换了一个不需要 SN 验证的 ffmpeg 执行文件。

在套件中心 - 设置 - 套件来源加入社区源: 

```bash
packages.synocommunity.com
```

信任任何人开发安装。

安装 ffmpeg 。

ssh 到 nas。

 ```bash
cp /volume1/@appstore/ffmpeg/bin/ffmpeg /volume1/@appstore/VideoStation/bin/
cp /volume1/@appstore/ffmpeg/bin/ffmpeg /usr/bin
 ```

## PhotoStation 支持视频缩略图 & 转码

> 相当于半洗白

1. 安装 docker 。

2. 按照要求安装 DDSM。
3. 进入 DDSM，在其信息中心有 序列号 & mac 地址。记录下来。
4. 修改 grup.cfg 中心 sn & mac

> 理论上只要修改 SN 即可，但是也推荐修改 mac 以免之后出现问题。
>
> 修改 mac 地址之后路由器会当做新的设备分配新的 IP。

## 完全关闭图片转码服务

为了加快 Photo Station 的浏览速度，群晖默认开启图片转码功能。其实在设置里可以设置相册不转换。或者在 Photo Station 内可以设置新相册不转码。如果有特殊的需求，可以在命令行关闭图片转码服务：

```bash
sudo synoservicecfg –stop synomkthumbd
```

这个关闭好像是永久的或者什么，反正我关了一次之后。之后重启也不会转码，之后忘记了还以为系统出问题了\_(:з」∠)\_。

## 关于完全洗白

据说在黑群晖上洗白，就算洗白了 ，不出几天就会被封号，对于普通(程序猿)用户来说。洗白之后的功能真的意义不大：局域网穿透、DDNS。所以一般半洗白，支持转码就基本 OK 了。

