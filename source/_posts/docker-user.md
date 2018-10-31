---
title: Docker 普通用户操作不用 sudo 权限
date: 2017-12-09 21:34:35
tags: Docker
---

Docker 的服务本来是要 root 权限或者 docker 组的权限。只要将当前用户加入 docker 组即可以在不使用`sudo` 的前提下运行 Docker 指令。

1. 新建 docker 组

   > 一般情况下，在安装 Docker 的时候已经创建好了。

```bash
sudo groupadd docker
```

2. 将当前用户加入 docker 组

```bash
sudo gpasswd -a ${USER} docker
```

​	或者

```bash
sudo usermod -aG docker ${USER}
```

3. 退出 ssh 或者重启 docker 服务

```bash
sudo systemctl restart docker
```
