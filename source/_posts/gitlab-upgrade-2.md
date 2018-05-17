title: GitLab 推送了更新(二) - 安装 docker GitLab
date: 2017-10-27 16:40:53
tags: [ Docker, GitLab ]
---

>之前在树莓派安装 GitLab 因为配置过低的原因，导致 GitLab 一直无法正常使用。
>痛并思痛，打算在一台`x86`的主机上安装 GitLab。
>当时想着能够快速部署而且不和现在的系统太过耦合，碰巧在 GitLab 主页上看到了小鲸鱼，想来试试这个已经出来好几年的新(liubi)技术。


## 环境
不知名 AMD 主机，4 核心， 12G DDR3 内存。

ubuntu 16.04.02 Desktop。

Docker 版本:

```
Docker version 1.12.6, build 78d1802
```

## 镜像安装

首先是要 pull 镜像。

因为官方的镜像速度实在是捉急，所以用了[DaoCloud Hub](http://hub.daocloud.io/)的国内镜像。

官方提供了一键安装脚本:
```bash
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://c8eb0d19.m.daocloud.io
```
然后执行 pull 命令

```bash
:#/ docker pull gitlab-ce 
```

拉去镜像之后, 可以通过:

```bash
:#/ docker images

REPOSITORY                              TAG                 IMAGE ID            CREATED             SIZE
gitlab/gitlab-ce                        latest              a77ba55a77f4        4 weeks ago         1.278 GB
```

显示镜像已经安装成功了。

## docker 实例化容器

总觉得 docker 启动要加一大堆参数，后来想想可以写 bash 命令。
于是从网上抄了一份启动命令。

```bash
docker run --detach \
    --hostname git.mapleincode.com \
    --publish 8443:443 --publish 5111:5111 --publish 2222:22 \
    --name gitlab \
    --restart always \
    --volume /data/gitlab/config:/etc/gitlab \
    --volume /data/gitlab/logs:/var/log/gitlab \
    --volume /data/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce
``` 

#### 参数说明

* `--detach`
	
	Run container in background and print container ID
* `--hostname`

	GitLab 的参数。hostname。
	
* `--publish`

	端口映射。从 docker 映射到 宿主机。
	
* `--name`

	运行的实名的名称。
	
	
* `--restart always`

	Restart policy to apply when a container exits (default "no")
	
* `--volume`

	文件夹映射。一般把需要持久化的文件【比如存储文件、配置文件】映射到本地来。
	
## 运行

然后给文件加上执行权限。运行后就启动了容易。

按照我的端口映射，访问了`http://192.168.2.110:5111`，直接跳转到了`root`登录。

GitLab 安装就直接完成了。

## 其他

1. 怎么进入到容器内。

	`sudo docker exec -it 容器id bash`
	
	`docker exec` 好像说是比较后期开发出的一个功能，用来执行和 `docker run` 不一样的函数执行。我一般 (zhihui) 用来启动 bash 来进入到容器中。
	比较方便的一点是，就算使用`exit`来退出容器，容器也不会停止运行。
	

2. GitLab 文件配置

	因为 docker 的原因，一般 GitLab 不会使用 22 作为默认的 ssh 端口。因此需要在`/etc/gitlab` 目录下，修改`gitlab.rb`文件。修改：
	`gitlab_rails['gitlab_shell_ssh_port'] = 21386`
	保存后重启容器即可。
	
3. GitLab 方便管理

	也没有很深入的去了解关于 GitLab 的管理，个人是去[DaoCloud](https://dashboard.daocloud.io)进行管理，启动关闭重启也很方便。
