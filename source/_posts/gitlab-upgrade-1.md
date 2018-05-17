title: GitLab 推送了更新(一) - 树莓派安装GitLab
date: 2017-10-26 11:16:52
tags: [ 树莓派, Ubuntu, GitLab ]
---

>家里常年挂了一个树莓派，因为噪音小(之前主机都放在卧室)，被动散热就可以满足，因此用来挂一些简单的脚本。
>之前`GitLab`整合了安装，推出了一键傻瓜式安装。后来版本更新到了`10.0`, 颜值比`8.0`要高。
>但是`arm`版本一直是`8.0`，上周`ssh`进去的时候，居然提示有更新，所以打算更新下树莓派上的`Gitlab`。


## 系统环境
RaspBerry B2

树莓派二代，四核`ARM`架构处理器, 1G DDR2 内存，做了 2GB 的虚拟缓存。

ubuntu 15.10 系统。

32G SD 卡。

## GitLab 8.0 

朋友推荐是安装到 2G 内存以上的机子，树莓派版本虽然做过一些优化，但是实际上 1G 内存运行还是有很大的压力。另个朋友建议增加虚拟缓存(swap)，冒着 SD 卡报废的风险，加了 2GB 虚拟缓存，但是因为 SD 的速度实在是慢，基本没什么用，但是 Gitlab 算是基本可以运行。
> We also strongly recommend at least 4GB of free memory to run GitLab.
> GitLab 官方如是说。

## 安装

1. 添加 gitlab apt 源

```bash
sudo curl -sS https://packages.gitlab.com/install/repositories/gitlab/raspberry-pi2/script.deb.sh | sudo bash
```
2. 安装 gitlab-ce

```bash
sudo apt-get install gitlab-ce
```

## deb 包安装

因为 GitLab 镜像到国内的速度实在是... 所以推荐自己下载 deb 包安装。

```bash
dpkg -i file.deb
```

## 最后

安装完...

发现...

树莓派...

根本 hold 不住，毕竟和推荐配置差太多了，基本一直处于`502`。

所以后来决定迁移数据到别的电脑，懵逼...
