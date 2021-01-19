---
title: 阿里云 Ubuntu 数据盘格式化和挂载
date: 2020-12-01 19:19:17
tags: [Ubuntu]
---

阿里云数据盘默认挂载之后不会分区、格式化、挂载。需要手动处理这个逻辑。

也适用于普通 Ubuntu 系统的情况。

## 1. 找到磁盘

```
fdisk -l
```

查找到所有磁盘和分区信息。

找到未分区或者未格式化的磁盘编号 `/dev/xxx`



## 2. 对磁盘进行分区

输入：
```
fdisk -u /dev/vdb
```

输入 `n` 创建一个新的分区
输入 `p` 选择创建分区类型为 primary

> BMR 分区表默认只支持 4 个主分区

输入 `1` 选择仅创建一个分区

然后选择第一个可用的扇区编号，我们这边直接回车，输入默认的 `2048`。

然后是输入最后一个扇区编号，我们这边依然回车，输入默认值。

然后输入 `p` 可以查看规划分区情况

最后输入 `w`，表示写入分区。

如果输入:

```
fdisk -lu /dev/vdb
```

应该会返回分区信息，如果存在 `/dev/vdb1` 说明分区已经成功

## 3. 格式化分区



- ext4

   ```
    mkfs -t ext4 /dev/vdb1
   ```

- xfs

  ```
  mkfs -t xfs /dev/vdb1
  ```

  



## 4. 写入启动配置

1. 通过 UUID 挂载

   ```
   cp /etc/fstab /etc/fstab.bak # 备份 fstab
   echo `blkid /dev/vdb1 | awk '{print $2}' | sed 's/\"//g'` /mnt ext4 defaults 0 0 >> /etc/fstab
   ```

   其中 `/mnt` 是你需要挂载磁盘的路径

2. 直接挂在 `/dev/vdb1`

   ```
   cp /etc/fstab /etc/fstab.bak # 备份 fstab
   echo "/dev/vdb1 /mnt ext4 defaults 0 0" >> /etc/fstab
   ```



然后通过 mount -a 挂载磁盘。

挂载的路径需要创建空文件夹，且文件夹中不能存在其他文件，否则会导致挂载失败。