---
title: 关于 grub2 的修复
date: 2019-02-21 10:49:48
tags: [ Linux ]
---

现在 Linux 的版本做的优化也是越来越到位，无论是功能，样式还是上手度都比以前好了很多。但是 Linux 的多样化造成经常一更新代码就挂掉。(表示更新 Fedora、OpenSUSE、Linux Mint 全部挂掉了)。不过因为相互兼容，所以重装也比较方便。但是以前重装 Windows 就不一样了。重装完 Windows 之后 Grub2 会被删的一干二净。

## windows 重装后导致 Grub 问题

找出以前整理过的一份笔记：

1. 首先是需要一个 Ubuntu 启动盘（U 盘 or 光盘）。进入系统。

2. 切换成 root 账户`su root`

3. 查看分区并挂载系统区。

   ```bash
   fdisk -l
   mount /dev/sda1 /mnt 
   ```

4. 在 MBR 安装 grub2

   ```bash
   grub-install --root-directory=/mnt /dev/sda
   ```

5. 重启系统。

   这时候之前安装的 grub 已经恢复。但是还是需要重新更新 grub2。

   ```bash
   sudo update-grub2
   ```

## 分区变动导致 Grub 问题

除了因为安装 Windows 导致的 Grub 问题。之前也遇到过一次因为分区被更改导致 Grub 丢失的问题。

特点是开机之后进入 Grub 显示

```bash
grub rescue>
```

解决方案如下：

首先查找 Grub 安装的分区

```bash
grub resuce> ls
(hd0),(hd0,msdos3),(hd0,msdos2),(hd0,msdos1)
```

查找办法是查看分区的 grub 文件夹是否存在

```bash
grub rescue> ls (hd0,msdos3)/boot/grub
```

查找到分区之后对其进行修复

```bash
grub rescue>set root=(hd0,msdos3)
grub rescue>set prefix=(hd0,msdos3)/boot/grub
grub rescue>insmod normal.mod
grub rescue>normal # 正常应该显示菜单栏。到这一步其实还没修复，只是临时被调用
```

然后进去系统后依然重新安装 Grub 并更新

```bash
sudo grub-install /dev/sda
sudo update-grub
```



## Grub 菜单的配置文件

```
/boot/grub/grub.cfg
```





最后感慨几点：

1. linux 的确有意思，功能性花样性
2. linux 各个发行版也都做的越来越好。性能也十分优秀。
3. mac os 真香。