title: Docker 普通用户操作不用 sudo 权限
date: 2017-12-09 21:34:35
tags: Docker
---

1. 新建 docker 组

```bash
sudo groupadd docker
```

2. 将当前用户加入 docker 组

```bash
sudo gpasswd -a ${USER} docker
```

或者

```bash
sudo usermod -aG docker ${USER}
```

3. 退出 ssh 或者重启 docker 服务

```bash
sudo systemctl restart docker
```
