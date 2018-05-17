title: Ubuntu 新增 swap
date: 2017-12-06 19:46:12
tags: Ubuntu
---
更换权限
`sudo su`

创建 swap 文件
`fallocate -l 1G /swapfile`

修改访问权限

```bash
chown root:root /swapfile
chmod 0600 /swapfile
```

启动 swap 分区

```bash
mkswap /swapfile
```

返回信息

```bash
Setting up swapspace version 1, size = 8 GiB (8589930496 bytes)
no label, UUID=f1f74673-d41b-4b06-a80c-15a6b449a1fe
```

激活 swap 分区

```bash
swapon /swapfile
```

然后修改开机载入
```bash
vim /etc/fstab
```

增加一行，如下：
```bash
/swapfile none            swap    sw              0      0
```
