---
title: 黑群晖 6.2.3 安装指南
date: 2020-11-13 16:48:42
tags: Synology
---

## 1. 准备设备

- nas 电脑设备一台
- 其他 PC 设备一台
- 启动 U 盘一个，推荐品牌 U 盘 <闪迪 USB2 16，狗东大概 20 左右>

## 2. 准备文件

- DSM_DS918+_25426.pat
  - 系统安装文件。
  - 可以官网直接下载。
  - 目前时间段最新的系统安装文件(2020-11-13)
  - 如果因为官网安装文件更新导致无法安装，则推荐手动下载安装文件用以安装
- DS918+_6.23-25426-1.04b（引导文件）.img
  - 引导文件
- DS918-6.23_1.04b的扩展驱动0.13.3.zip
  - 驱动扩展，用于增强驱动，支持 2.5G 网卡
- libsynonvme(918+623).zip
  - 黑群辉支持 nvme 配置文件



## 3. 准备软件

- ChipEasy 芯片无忧
- DiskGenius
- Win32 Disk Imager



## 4. 制作启动盘

首先使用 Win32 Disk Imager 把 `synoboot.img` 文件写到 u 盘里。然后就是修改启动盘。

打开 ChipEasy ，找到当启动盘的那个 U 盘，查看它的设备 ID。

一般包含 VID 和 PID。记录两者。

> 有些劣质的 U 盘可能会缺失这两个信息，建议最好还是换一个 U 盘。



用 DiskGenius 打开 U 盘，然后找到 ESP 下的 grub，点击 grup。会出现若干个文件夹和文件。其中有一个 `grup.cfg` 。右键该文件，选择"复制到桌面”。



用 vscode 修改 grup.cfg。搜索 mac1 找到类似:

```
set vid=0x058f
set pid=0x6387
set sn=A8ODN02468
set mac1=0011322CA603
```

修改 vid 和 pid 为从 ChipEasy 中读取的 VID 和 PID。**第一次安装请不要修改 mac1 !，否则可能导致安装时找不到 nas。**

最后把 `grup.cfg` 复制到 U 盘中的 grup，并选择覆盖源文件。

这里注意一点是修改后的文件大小应该和源文件一致，如果出现修改后的文件大小不等于源文件的大小，肯定是不小心修改了多余的地方。建议重新把源文件复制到桌面进行修改。



## 5. 安装

把 U 盘插到 nas 上。选择 U 盘启动。

正常会出现类似以下文字

```
Insto:
This mod is brought to you .....
```

一大堆黑底白字。

这时候有两个办法，一个办法是访问 [http://find.synology.com/](http://find.synology.com/)，然后搜索安装群晖系统。

另个办法是下载 SynologyAssistant 进行搜索。

> SynologyAssistant 比前者相对靠谱点。



安装过程中，nas 必须包含一个或以上的硬盘。

**！！！！ 硬盘会被格式化，请勿在硬盘中保存资料！！！！**

然后无脑安装就好了。

注意一点是，因为最新的引导是支持最新的系统镜像。所以选择"立即安装" 也不会出现任何问题(也就是自动下载安装最新的镜像)。但如果新版本的群晖发布，你使用的引导不支持，就会出现安装失败。这时候你就得选择手动安装，然后上传 `DSM_DS918+_25426.pat`。

安装进度条达到 100 % 一百的时候会自动进行重启。**期间重启时间可能稍长，请勿切断电源！**



## 6. 安装完成

然后我们就可以进入群晖界面了，对于大部分情况而言，这个 nas 基本是安装完成了，除了少数功能：

1. PhotoStation 和 Moment 不支持图片缩略图、视频预览封面。
2. Video 不支持转码
3. 不支持 NVME SSD 加速



在出现 QuickConnect ID 注册界面，请选择"跳过此步骤"，请勿注册 Quick Connect ID。

## 7. 功能洗白

**功能洗白为半洗白，仅为了支持转码和缩略图功能，请勿用于Quick Connect ID 创建！**

把启动 U 盘拔出重新插到 windows 上，还是打开原来  grup.cfg。找到 `mac1` (之前修改 pid、vid 的地方)。

```
set sn=A8ODN02468
set mac1=0011322CA603
```

替换 sn 和 mac1。然后按照原来的方法把  grup.cfg 重新覆盖到源文件。

然后用引导 U 盘重启 nas。

> 因为 mac 地址被修改，所以你第一次访问的 IP 地址可能会变成新的 IP。这时候可以通过  http://find.synology.com 或者 SynologyAssistant 查找 nas。还有个简单的办法是直接看路由器的 dhcp 分配记录。

到此为止，黑群辉功能洗白成功。可以正常支持缩略图和图片、视频转码功能。



## 8. 支持大内存

默认情况，黑群辉的引导盘只支持 3G 内存，这个不知道是不是有 BUG。但是看到自己 8G 内存，只有 3G 可以正常使用，而剩下的 5G 内存处于"保留"还是觉得难受。

解决办法，依然是修改 grup.cfg，找到 `set common_args_918`，修改冒号里的内容，加入一个指令:

```
disable_mtrr_trim
```

 例如我使用的 918 引导，修改后为：

```
set common_args_918='disable_mtrr_trim syno_hdd_powerup_seq=1 HddHotplug=0 syno_hw_version=DS918+ vender_format_version=2 console=ttyS0,115200n8 withefi elevator=elevator quiet syno_hdd_detect=0 syno_port_thaw=1'
```

老方法保存，重启 nas，内存占用就正常了。



## 9. NVME 支持

> 黑群辉下 nvme 只能作为缓存盘，用于加速。不能用于存储。
>
> msata 和 ngff 的 SSD 不受影响。
>
> 注意 ngff 和 nvme 的区别。
>
> 看过一个文章提到说有个评测证明，sata SSD 作为 SSD 缓存对性能的提升极小。但是现在 nvme SSD 和 sata SSD 价格差不多的情况下，还是建议选择 nvme  SSD 作为 SSD 缓存。

默认情况下，黑群辉是不支持 nvme，据说是因为群晖默认 nvme 端口号是写死的。解决办法是使用 `libsynonvme(918+623).zip` 文件。

> 这个仅支持 6.23 。6.22 也有对应的办法，但是使用的配置文件不一样。

解压之后得到一个 `libsynonvme.so.1`，把这个文件放到 `home` 文件夹中。



在群晖上开启 SSH 功能。然后实用 SSH 登录 nas。找到存放在 nas 的文件，复制到`/usr/lib/libsynonvme.so.1`。

```
# 先备份原来的 so 文件，以免出现问题
sudo mv /usr/lib/libsynonvme.so.1 /usr/lib/libsynonvme.so.1.bak
sudo cp ~/libsynonvme.so.1 /usr/lib/libsynonvme.so.1
```

> 输入 sudo 后需要输入你登录 nas 的密码。

重启 nas 后，nvme 正常识别。





## 10. 2.5G 网卡支持

我使用的主板是华硕的 B550，网卡是 2.5G。默认群晖是不支持 2.5G 网卡的。但是有国外大佬做的一个驱动增强包`DS918-6.23_1.04b的扩展驱动0.13.3.zip`。

解压扩展包，得到两个文件：

```
extra.lzma
extra2.lzma
```



还是用 DiskGenius 打开启动 U 盘。在左侧目录下可以看到一个 `分区(1)`，右键选择"挂载到 windows"。然后选择确定之后。可以发现这个分区出现在文件管理那边。然后里面包含 `extra.lzma` 若干个文件。

把从扩展包里得到的 lzma 文件拖进去，注意不能同时拖两个文件，因为 windows 的覆盖机制，会报错容量不足。如果一个一个拖就不会有这样的问题。

然后退出 U 盘，重新启动，就能支持 2.5G 网卡了。



但是这仅限于已经安装好的系统，orz。



对于如果没有安装过系统的硬盘，是不能直接在 2.5G 网卡主板上直接安装黑群辉的。所以我们还需要额外的一台电脑设备用于安装群晖系统。

首先在另外一台设备上安装完群晖，进入系统，洗白等等操作。等完成之后，再把扩展包复制到启动盘里去。然后再把启动盘插到 2.5G 主板的主机中，并附加已经安装过系统的硬盘，然后启动。

到此为止就可以正常访问 2.5G 的 nas 了。

