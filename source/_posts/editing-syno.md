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





## 制作镜像

首先获取 nas 的 mac 并记录。获取办法建议通过路由器。



##  PhotoStation 支持视频缩略图 办法 1

 ```bash
cp /volume1/@appstore/ffmpeg/bin/ffmpeg /volume1/@appstore/VideoStation/bin/
cp /volume1/@appstore/ffmpeg/bin/ffmpeg /usr/bin
 ```



## 完全关闭图片转码

为了加快 Photo Station 的浏览速度，群晖默认开启图片转码功能。其实在设置里可以设置相册不转换。或者在 Photo Station 内可以设置新相册不转码。如果有特殊的需求，可以在命令行关闭图片转码服务：

```bash
sudo synoservicecfg –stop synomkthumbd
```

这个关闭好像是永久的或者什么，反正我关了一次之后。之后重启也不会转码，之后忘记了还以为系统出问题了\_(:з」∠)\_。

