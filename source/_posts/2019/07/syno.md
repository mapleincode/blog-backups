---
title: 黑群晖的安装 & 洗白
date: 2019-03-29 11:08:23
tags: [ synology ]
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

> 已失效

## 安装群晖系统

### 1. 首先获取 nas 设备的 mac。

mac 地址相当于是网卡的唯一编号。最简单的办法就是看下路由器。

### 2. 获取启动 U 盘的 vid 和 cid 。

windows 环境通过 `ChipEasy` 获取。

linux 上可以通过 `lsusb` 指令获取。

> 前同事之前买了一个号称无比便宜的 U 盘。结果发现里面没有 `vid` 和 `cid`。
>
> 因为启动 U 盘需要作为引导，需要一直插在 nas 上。
>
> 推荐购买闪迪的酷豆，价格也便宜，最主要小巧。
>
> ![O1CN01qdRZDD1IOud3Pl6Jp_!!2616970884](https://raw.githubusercontent.com/mapleincode/images/master/img/20200516063418.jpg)
>
> 而容量只要有 100MB 就绰绰有余了。

### 3. 写入启动文件

用 Win32DiskImager 把 syno 启动镜像 img 文件写入到 U 盘。

这个步骤太简单了，选取 bin 文件。然后选取有盘盘符，然后写入。



### 4. 修改 grup.cfg

打开 DiskGenius ，找到 U盘目录下 `ESP[0]/grup/grup.cfg` 。

注意一点是，文件是不会出现在左边的文件框中，而是在右边。

![image-20200516063738659](/Users/maple/Library/Application Support/typora-user-images/image-20200516063738659.png)

右键复制到桌面。

![image-20200516063751842](/Users/maple/Library/Application Support/typora-user-images/image-20200516063751842.png)

然后用文本编辑软件打开，修改 vid 、cid 、mac 地址。(搜索下 mac 就能定位地址)。

![image-20200516062928427](https://raw.githubusercontent.com/mapleincode/images/master/img/20200516062928.png)

最后把将 cfg 文件覆盖回 U 盘。

### 5. 安装系统

u 盘插入 nas ，就会进入到愉快的安装界面。

这里有一点需要注意，nas 的启动盘因为是破解的，所以无法按照启动盘之后的文件。



例如我一个启动盘支持的是 6.2 23824。那我系统也只能安装 23824 并且要对应启动盘的设备版本。

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

