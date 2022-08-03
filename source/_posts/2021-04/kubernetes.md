---
title: "kubernetes 学习"
tags: [ kubernetes, docker ]
date: 2021-04-12 16:12:11
---

[TOC]

# kubernetes

核心是容器。



## 1. 工具 & 环境

参考：https://kubernetes.io/zh/docs/tasks/tools/

- 工具
  - kubectl: 管理工具，相当于管理 client 端。
  - kubeadm: 相当于 server 端。
- 服务器
  - 两台 4 核心 4G 的虚拟机

> 每台机器 2 GB 或更多的 RAM （如果少于这个数字将会影响你应用的运行内存)



> 本地开发推荐 minikube 和 kind 这两个工具来实现本地运行测试和开发 Kubernetes。我因为开发电脑配置偏低，所以采用远程虚拟机的方式进行学习。



## 2. 安装



### 2.1 管理端工具安装

#### 安装 kubectl (macOS)

> 命令行管理工具

1. brew 安装

   ```
   brew install kubectl
   ```

2. 直接安装

   ```
   # 下载最新版本
   curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"
   
   # + 执行权限
   chmod +x ./kubectl
   
   // 移动到 bin 目录
   sudo mv ./kubectl /usr/local/bin/kubectl
   
   // 授予 root 组权限
   sudo chown root: /usr/local/bin/kubectl
   ```



### 2.2 服务端安装

> Pod

#### 2.2.1 Docker

考虑到 kubeadm 初始化会检测 Docker，所以考虑先安装 Docker。

```
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```



##### 1. 镜像加速

> --mirror Aliyun 使得安装时采用阿里云源，减少安装时间

阿里云有提供一个速度还算不错的 docker  镜像加速器。个人可以免费使用。

在控制台搜索 < 容器镜像服务 >，选择 - 镜像工具 - 镜像加速器。按照配置镜像加速器的方式进行配置。



##### 2. 默认账户没有 docker 权限问题

默认 Docker sock 需要 root 权限才能访问，在本地开发测试，可以使用:

```
sudo usermod -aG docker ${USER}
```

把用户 user 也加入到 docker 组。退出 ssh 或者退出登录重进，就可以在不使用 sudo 的前提下使用 docker。



#### 2.2.2 kubeadm

> Pod 工具

官方教程需要在 ubuntu apt 安装一个授权 key，但是域名是 *.google.com

这决定了很多 linux 环境，尤其是生产都很难方面的安装。后来在网上搜到了一份教程，作者通过替换镜像等方式解决这个问题。



更新 apt-key & 添加依赖库，这里添加的是阿里云提供的库。

```
apt-get update && apt-get install -y apt-transport-https
curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add - 
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
```

**这里注意是 xenial ，虽然 xenial 是 16.04。而作为最新 LTS 18.04 和 20.04，aliyun 都没有更新。实际操作并无影响，不需要额外替换。apt 可以正常识别。**



#### 2.2.3 安装 kubeadm / kubectl / kubelet

```
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```



> 引用教程作者原话：
>
> 如果你有多台机器，非 master 节点不需要安装 kubeadm/kubectl。当然装了也没啥坏处
>
> apt-mark hold 是为了标记 kubelet 不自动升级
>
> 可以选择 apt-mark unhold kubelet  取消标记
>
> 

#### 2.2.4 pull kubernetes 镜像

> 国外的朋友可以跳过这一步

如果在国内，直接运行`kubeadm init` 会一直卡主不懂。原因是因为默认镜像拉取的是谷歌源。

```
$ sudo kubeadm init
[init] Using Kubernetes version: v1.21.0
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
```



解决办法是用国内的 docker 镜像加速。

**第一种版本是手动替换：**

1. 首先查看 kubelet 版本：

```
$ kubelet --version
Kubernetes v1.21.0
```

2. 使用 kubeadm config image pull 命令查看所需要 image 名称和版本:

```
$ kubeadm config images list --kubernetes-version v1.21.0
k8s.gcr.io/kube-apiserver:v1.21.1
k8s.gcr.io/kube-controller-manager:v1.21.1
k8s.gcr.io/kube-scheduler:v1.21.1
k8s.gcr.io/kube-proxy:v1.21.1
k8s.gcr.io/pause:3.4.1
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns/coredns:v1.8.0
```

3. 这里要手动替换 `k8s.gcr.io` 为 `k8smx`，并使用 docker pull 的方式下载镜像。

```
docker pull k8smx/kube-apiserver:v1.21.1
docker pull k8smx/kube-controller-manager:v1.21.1
docker pull k8smx/kube-scheduler:v1.21.1
docker pull k8smx/kube-proxy:v1.21.1
docker pull k8smx/pause:3.4.1
docker pull k8smx/etcd:3.4.13-0
docker pull coredns/coredns:1.8.0
```

> coredns/coredns 直接拉取，要注意 version 需要从 v1.8.0 改成 1.8.0 否则 pull 不下来

4. 然后对镜像 tag 进行重命名

```
docker tag k8smx/kube-apiserver:v1.21.1 k8s.gcr.io/kube-apiserver:v1.21.1
docker tag k8smx/kube-controller-manager:v1.21.1 k8s.gcr.io/kube-controller-manager:v1.21.1
docker tag k8smx/kube-scheduler:v1.21.1 k8s.gcr.io/kube-scheduler:v1.21.1
docker tag k8smx/kube-proxy:v1.21.1 k8s.gcr.io/kube-proxy:v1.21.1
docker tag k8smx/pause:3.4.1 k8s.gcr.io/pause:3.4.1
docker tag k8smx/etcd:3.4.13-0 k8s.gcr.io/etcd:3.4.13-0
docker tag coredns/coredns:1.8.0 k8s.gcr.io/coredns/coredns:v1.8.0
```



**第二种办法是使用自动下载脚本:**

```shell
for i in `kubeadm config images list`; do
  imageName=${i#k8s.gcr.io/}
  docker pull registry.aliyuncs.com/google_containers/$imageName
  docker tag registry.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
  docker rmi registry.aliyuncs.com/google_containers/$imageName
done;
```

> coredns/coredns 镜像无法通过自动操作，需要手动

```
docker pull coredns/coredns:1.8.0
docker tag coredns/coredns:1.8.0 k8s.gcr.io/coredns/coredns:v1.8.0
```

### 2.3 安装过程可能遇到的问题



##### cproupfs 和 systemd 两个驱动的区别

在初始化过程中，会产生一个 WARNING:

```
[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". 
```



大概意思是默认 Docker 在系统中使用了 cgroupfs 这个 cgroup 驱动（管理），而 kube 推荐使用 systemd。

cgroupfs 和 systemd 都是 cgroup 的管理工具（驱动）。systemd 作为后来者，功能点可能比前者更加完善（然而我查不到相关的中文资料）。因为 kubernetes 会默认使用 systemd 作为 cgroup 管理。如果用户不做修改，默认 docker 和 kubernetes 会使用 cgroupfs 作为 cgroup 作为管理。则这个系统会出现两种 cgroup 管理工具（驱动）。



官方认为，如果系统包含两种工具，可能在资源紧张时会导致系统不稳定。所以推荐用户把 docker 和 kubernetes 的 cgroup 修改为 systemd。



**修改需要同时修改 docker 和 kubelet 的配置，否则会造成无法通过 kubelet 调度容器。**

**docker**:

修改方式是在 /etc/docker/daemon.json 新增一条参数

```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```



修改后重启 docker `systemctl restart docker`。使用 kubeadm init 就不会产生这个  WARNING 了。



**kubelet**:

修改 `/var/lib/kubelet/config.yaml` 的 cgroupDriver 改为 `systemd`

>强烈建议不要更改已加入集群的节点的cgroup驱动程序。
>
>如果kubelet使用一个cgroup驱动程序的语义创建了Pods，那么在试图为现有的Pods重新创建Pod沙箱时，将容器运行时更改为另一个cgroup驱动程序可能会导致错误。重新启动kubelet可能无法解决这些错误。



参考: https://www.codenong.com/cs109603870/



##### 关闭 swap

在使用命令 `kubelet init` 时，发生报错:

```
[ERROR Swap]: running with swap on is not supported. Please disable swap
```

原因是我的虚拟机开启了 swap。而 swap 对虚拟机而言会造成额外的性能问题。所以理所当然要关闭 swap。当然你可以可以通过 `ignore` 忽略。



## 3. kubeadm init

> 初始化 kubernetes



在安装完所有依赖的 docker 镜像之后，我们就可以执行初始化。

```
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/
```



在最后完成之后会弹出一个

```
Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.3.242:6443 --token plr2xt.a19ifb79gl4l3uxr \
	--discovery-token-ca-cert-hash sha256:f1d2929b4ce2414dabfe5b37ba740a22dd320099df13168e06cc245c380e4176
```

适用于其他节点加入集群。



这边我犯了一个错误，其实有且只有一个节点需要 `kubeadm init`。而第二个节点不需要。如果第二个节点执行 `kubeamdin join xxx` 会发生报错。

参考了 gayhub 的方案: [https://github.com/kubernetes/kubeadm/issues/974#issuecomment-402861544]()

只需要执行 ：

```
kubeadm reset
```

选择 `Y` 就可以初始化 kubeadm。

```
The reset process does not clean CNI configuration. To do so, you must remove /etc/cni/net.d

The reset process does not reset or clean up iptables rules or IPVS tables.
If you wish to reset iptables, you must do so manually by using the "iptables" command.

If your cluster was setup to utilize IPVS, run ipvsadm --clear (or similar)
to reset your system's IPVS tables.

The reset process does not clean your kubeconfig files and you must remove them manually.
Please, check the contents of the $HOME/.kube/config file.
```



## kubeadm  join

输入第一台服务器返回的`kubeadm join xxxx`。

返回：

```
To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.3.242:6443 --token 17s2zk.29hdphqfeerxkm18 \
	--discovery-token-ca-cert-hash sha256:90adf2e95cb044d436c6d4849ce2804050adb9db0c7168585dd0522ccb201fbf
```

然后在第一台服务器输入

```
kubectl get nodes
```

返回：

```
NAME            STATUS     ROLES                  AGE     VERSION
vm-ubuntu-201   NotReady   control-plane,master   7m17s   v1.21.1
vm-ubuntu-202   NotReady   <none>                 3m25s   v1.21.1
```



## 参考

- [Kubernetes 文档](https://kubernetes.io/zh/docs/home/)
- [在 macOS 系统上安装和设置 kubectl](https://kubernetes.io/zh/docs/tasks/tools/install-kubectl-macos/)
- [安装 kubeadm](https://kubernetes.io/zh/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Docker 安装 (阿里云教程)](https://developer.aliyun.com/article/110806)
- [墙内安装kubernetes教程](https://ieevee.com/tech/2018/09/01/kubeadm.html)
- [Cgroup drivers：systemd cgroupfs 区别](https://www.codenong.com/cs109603870/)

