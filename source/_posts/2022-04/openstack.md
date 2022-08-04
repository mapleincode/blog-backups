---
title: OpenStack 学习
date: 2022-04-30 10:43:10
---

[TOC]

## 关键术语

**访问密钥/安全密钥**

组合使用来访问和与一个计算实例通讯。安全密钥用于加密每次请求。

> 用户 pub 密钥



**可用区域**

云范围内用于对虚拟机提供容错的一组隔离的虚拟层。



**块存储**

存储类型，目的是提供挂在到虚拟机上的持久化存储，它支持卷，卷快照和卷类型管理。



**容器（对象）**

在对象存储中组织和存储对象。



**容器（LXC）**

Linux容器是在单个Linux宿主上运行多个相互隔离的Linux系统的操作系统级别的虚拟环境。Openstack容器服务（Magnum）提供应用容器的管理功能。



**固定/静态IP**

相同VM（虚拟机）每次启动的时候关联的IP地址。这可用于管理VM，但用户通常不能访问它。



**配置**

虚拟机镜像的一组参数，包括CPU占用率，内存大小，存储空间大小，等等。



**浮动IP**

与虚拟机实例关联的公开的IP地址，这样该实例每次启动的时候其公开的IP地址都相同。



**镜像**

需要用它来创建或者重建服务器的某个特定的操作系统的文件集合。Openstack支持多种镜像格式（AMI，VMDK，VHD，QEMU）



**网络**

在实体之间提供第2层网络连接的虚拟网络。



**对象**

对象是存储在对象存储中的任意类型的数据，它可以是任意格式的数据——文件、音乐、视频，或者二进制数据。



**对象存储**

存储类型，它终于支持一致的、冗余的和非结构化的数据的存储。



**项目/租户**

逻辑用户组，通常也称为租户。



**配额**

以项目为单位设置的计算和存储资源的限额。



**角色**

一个角色拥有一个权限集。某角色的用户继承角色的所有权限。



**安全组**

应用于计算实例的一组过滤规则。



**服务**

提供用户通过其访问和控制资源的某个Openstack服务。



**快照**

存储卷或者镜像在某个时间点的拷贝。



**用户**

项目/租户消费云资源的一份子。



**卷**

基于磁盘的数据存储，通常表现为支持扩展属性的iSCSI设备。这种存储可能是持久的或短暂的（重启后丢失）。





## 虚拟机创建过程

1. 界面或命令行通过 RESTful API 向 keystone 获取认证信息。
2. keystone通过用户请求认证信息，并生成 auth-token 返回给对应的认证请求。
3. 界面或命令行通过 RESTful API 向 nova-api 发送一个 boot instance 的请求（携带auth-token）。
4. nova-api 接受请求后向 keystone 发送认证请求，查看 token 是否为有效用户和 token。
5. keystone 验证 token 是否有效，如有效则返回有效的认证和对应的角色（注：有些操作需要有角色权限才能操作）。
6. 通过认证后 nova-api 和数据库通讯。
7. 初始化新建虚拟机的数据库记录。
8. nova-api 通过 rpc.call 向 nova-scheduler 请求是否有创建虚拟机的资源(Host ID)。
9. nova-scheduler 进程侦听消息队列，获取 nova-api 的请求。
10. nova-scheduler 通过查询 nova 数据库中计算资源的情况，并通过调度算法计算符合虚拟机创建需要的主机。
11. 对于有符合虚拟机创建的主机，nova-scheduler 更新数据库中虚拟机对应的物理主机信息。
12. nova-scheduler 通过 rpc.cast 向 nova-compute 发送对应的创建虚拟机请求的消息。
13. nova-compute 会从对应的消息队列中获取创建虚拟机请求的消息。
14. nova-compute 通过 rpc.call 向 nova-conductor 请求获取虚拟机消息。（Flavor）
15. nova-conductor 从消息队队列中拿到 nova-compute 请求消息。
16. nova-conductor 根据消息查询虚拟机对应的信息。
17. nova-conductor 从数据库中获得虚拟机对应信息。
18. nova-conductor 把虚拟机信息通过消息的方式发送到消息队列中。
19. nova-compute 从对应的消息队列中获取虚拟机信息消息。
20. nova-compute 通过 keystone 的 RESTfull API 拿到认证的 token，并通过HTTP请求 glance-api 获取创建虚拟机所需要镜像。
21. glance-api 向 keystone 认证 token 是否有效，并返回验证结果。
22. token验证通过，nova-compute 获得虚拟机镜像信息(URL)。
23. nova-compute 通过 keystone 的 RESTfull API 拿到认证 k 的 token，并通过 HTTP 请求 neutron-server 获取创建虚拟机所需要的网络信息。
24. neutron-server 向 keystone 认证 token 是否有效，并返回验证结果。
25. token 验证通过，nova-compute 获得虚拟机网络信息。
26. nova-compute 通过 keystone 的 RESTfull API 拿到认证的 token，并通过 HTTP 请求 cinder-api 获取创建虚拟机所需要的持久化存储信息。
27. cinder-api 向 keystone 认证 token 是否有效，并返回验证结果。
28. token 验证通过，nova-compute 获得虚拟机持久化存储信息。
29. nova-compute 根据 instance 的信息调用配置的虚拟化驱动来创建虚拟机。



## 新增镜像

> CirrOS是一个小型的Linux镜像。



```bash
$ . admin-openrc # 授权
$ wget http://download.cirros-cloud.net/0.3.4/cirros-0.3.4-x86_64-disk.img
$ openstack image create "cirros" \
  --file cirros-0.3.4-x86_64-disk.img \
  --disk-format qcow2 --container-format bare \
  --public
+------------------+------------------------------------------------------+
| Property         | Value                                                |
+------------------+------------------------------------------------------+
| checksum         | 133eae9fb1c98f45894a4e60d8736619                     |
| container_format | bare                                                 |
| created_at       | 2015-03-26T16:52:10Z                                 |
| disk_format      | qcow2                                                |
| file             | /v2/images/cc5c6982-4910-471e-b864-1098015901b5/file |
| id               | cc5c6982-4910-471e-b864-1098015901b5                 |
| min_disk         | 0                                                    |
| min_ram          | 0                                                    |
| name             | cirros                                               |
| owner            | ae7a98326b9c455588edd2656d723b9d                     |
| protected        | False                                                |
| schema           | /v2/schemas/image                                    |
| size             | 13200896                                             |
| status           | active                                               |
| tags             |                                                      |
| updated_at       | 2015-03-26T16:52:10Z                                 |
| virtual_size     | None                                                 |
| visibility       | public                                               |
+------------------+------------------------------------------------------+
```



```shell
openstack image create
```

等同于

```
glance image-create
```



### 命令

```
glance image-create 
--name image_name 
--disk-format disk_format 
--container-format container_format 
--is-public [True|False] 
< image_path
```

其中

- **image_name**

  指定您要添加的新映像的名称。

- **disk_format**

  指定下列其中一种磁盘格式:

  - **raw** 非结构化的磁盘映像格式。
  - **qcow2** QEMU 仿真器支持的磁盘格式，该格式可以动态地扩展并支持写入时复制。
  - **vmdk** 对于 VMware 系统管理器，这是许多常见虚拟机监视器支持的另一种常见的磁盘格式。

- **container-format**

  指定映像的容器格式。 可接受的格式如下：aki、ami、ari、bare 和 ovf。

- **--is-public**

  指定映像是否可供其他用户访问。 值可以为 true 或 false。 如果您指定 false 值，那么映像只能在 /root/openrc 文件中指定的项目作用域内进行访问。

- **image_path**

  指定要添加的映像的完整路径。



## 关于 qcow2 和 raw

**raw**

- 格式简单，性能较好
- 不支持稀疏格式，最好基于 ext3

```
dd if=/dev/zero of=flat1.img bs=1024k count=1000 # 非稀疏文件
dd if=/dev/zero of=flat2.img bs=1024k count=0 seek=2048 # 稀疏文件

# 稀疏文件
dd if=/dev/zero of=flat3.img bs=1024k count=1 seek=2048
cp --sparse=never flat3.img flat3-never.img
cp --sparse=always flat3.img flat3-always.img
cp --sparse=always flat3-never.img flat3-never-always.img
```

> 文件空洞，在UNIX文件操作中，文件位移量可以大于文件的当前长度。如果 offset 比文件的当前长度更大，下一个写操作就会把文件“撑大（extend）” 在文件里创造“空洞（hole）”。
>
> 稀疏文件与其他普通文件基本相同，区别在于文件中的部分数据是全0，且这部分数据不占用磁盘 空间。



**qcow2**

- 即便文件系统不支持sparse file，文件大小也很小
- copy on write

```bash
# 创建backing file (cow)
qemu-img create -f qcow2 -o backing_file=./ubuntutest.qcow2 ubuntutest1.qcow2

# 基于ubuntutest1.img创建一个虚拟机
qemu-system-x86_64 -enable-kvm -name ubuntutest  -m 2048 -hda ubuntutest1.qcow2 -vnc :19 -net nic,model=virtio -net tap,ifname=tap0,script=no,downscript=no -monitor stdio

# img -> qcow2
dd if=/dev/zero of=flat.img bs=1024k count=1000
qemu-img convert -f raw -O qcow2 flat.img flat.qcow2

# qcow2 -> qcow2
qemu-img convert -f qcow2 -O qcow2 ubuntutest.qcow2 ubuntutest-convert.qcow2

# 压缩
qemu-img convert -c -f qcow2 -O qcow2 ubuntutest.img ubuntutest-compress.qcow2
```



## Disk and Container Formats

> https://docs.openstack.org/glance/pike/user/formats.html

When adding an image to Glance, you must specify what the virtual machine image’s *disk format* and *container format* are. 

在向 Glance 添加镜像时，必须要先指定虚拟机镜像的磁盘格式和容器格式。

Disk and container formats are configurable on a per-deployment basis. This document intends to establish a global convention for what specific values of *disk_format* and *container_format* mean.

磁盘和容器格式可在每次部署的基础上进行配置。本文档旨在为*disk_format*和 *container_format*的特定值的含义建立一个全球约定。

### Disk Format

The disk format of a virtual machine image is the format of the underlying disk image. Virtual appliance vendors have different formats for laying out the information contained in a virtual machine disk image.

虚拟机映像的磁盘格式是底层磁盘映像的格式。虚拟设备供应商有不同的格式来布置虚拟机磁盘映像中包含的信息。

You can set your image’s disk format to one of the following:

- **raw**

  This is an unstructured disk image format

  这是一种非结构化磁盘映像格式

- **vhd**

  This is the VHD disk format, a common disk format used by virtual machine monitors from VMware, Xen, Microsoft, VirtualBox, and others

  这是 VHD 磁盘格式，VMware、Xen、Microsoft、VirtualBox 等虚拟机服务使用的常见磁盘格式

- **vhdx**

  This is the VHDX disk format, an enhanced version of the vhd format which supports larger disk sizes among other features.

  这是 VHDX 磁盘格式，是 vhd 格式的增强版本，它支持更大容量的磁盘以及其他新的功能。

- **vmdk**

  Another common disk format supported by many common virtual machine monitors

  另一种多种通用虚拟机服务支持的通用磁盘格式

- **vdi**

  A disk format supported by VirtualBox virtual machine monitor and the QEMU emulator

  VirtualBox 虚拟机监视器和 QEMU 模拟器支持的磁盘格式

- **iso**

  光盘数据内容的存档格式（例如 CDROM）。

- **ploop**

  A disk format supported and used by Virtuozzo to run OS Containers

  Virtuozzo 支持并用于运行 OS 容器的磁盘格式

- **qcow2**

  A disk format supported by the QEMU emulator that can expand dynamically and supports Copy on Write

  QEMU模拟器支持的一种磁盘格式，可以动态扩展，支持写入时复制

- **aki**

  这表明 Glance 中存储的是 Amazon 内核映像

- **ari**

  这表明 Glance 中存储的是 Amazon ramdisk 映像

- **ami**

  这表明 Glance 中存储的是 Amazon 机器映像



### Container Format

The container format refers to whether the virtual machine image is in a file format that also contains metadata about the actual virtual machine.

Note that the container format string is not currently used by Glance or other OpenStack components, so it is safe to simply specify **bare** as the container format if you are unsure.

容器格式是指虚拟机映像是否采用还包含有关实际虚拟机的元数据的文件格式。

请注意，Glance 或其他 OpenStack 组件当前不使用容器格式字符串，因此如果您不确定，可以简单地将**bare指定为容器格式。**

You can set your image’s container format to one of the following:

您可以将镜像的容器格式设置为以下之一：

- **bare**

  This indicates there is no container or metadata envelope for the image

  这表示镜像没有使用容器或元数据封装

- **ovf**

  This is the OVF container format

  这是 OVF 容器格式

- **aki**

  This indicates what is stored in Glance is an Amazon kernel image

  这表明 Glance 中存储的是 Amazon 内核镜像

- **ari**

  This indicates what is stored in Glance is an Amazon ramdisk image

  这表明 Glance 中存储的是 Amazon ramdisk 镜像

- **ami**

  This indicates what is stored in Glance is an Amazon machine image

  这表明 Glance 中存储的是 Amazon 机器镜像

- **ova**

  This indicates what is stored in Glance is an OVA tar archive file

  这表明 Glance 中存储的是 OVA tar 归档文件

- **docker**

  This indicates what is stored in Glance is a Docker tar archive of the container filesystem

  这表明 Glance 中存储的是容器文件系统的 Docker tar 归档文件



##  启动一个实例



### 1. 创建网络



#### 创建提供者网络

> **Provider networks**

1. 授权

   ```shell
   $ . admin-openrc
   ```

2. 创建网络：

   ```shell
   $ openstack network create  --share --external \
     --provider-physical-network provider \
     --provider-network-type flat provider
   ```

3. 创建子网

   ```shell
   $ openstack subnet create --network provider \
     --allocation-pool start=192.168.201.200,end=192.168.201.210 \
     --dns-nameserver 114.114.114.114 --gateway 192.168.201.1 \
     --subnet-range 192.168.201.0/24 provider
   ```




### 2. 生成一个密钥对

> 可以通过控制台直接上传.
>
> 控制台上传的密钥和管控台上传的独立，研究 ing

```shell
$ . demo-openrc
$ ssh-keygen -q -N ""
$ openstack keypair create --public-key ~/.ssh/id_rsa.pub mykey

$ openstack keypair list # 验证
```



### 3. 增加安全组规则

> 默认存在 default 安全组



```shell
$ openstack security group rule create --proto icmp default # Permit ICMP (ping)
$ openstack security group rule create --proto tcp --dst-port 22 default # 允许安全 shell (SSH) 的访问
```



## 4. 在公有网络上创建实例



```shell
$ openstack flavor list         # 查看可用类型
$ openstack image list          # 查看可用镜像
$ openstack network list        # 查看可用的网络 -----> 获取 PROVIDER_NET_ID
$ openstack security group list # 查看可用安全组

$ openstack server create --flavor m1.nano --image cirros \
  --nic net-id="a2197965-1a9e-4584-892e-c38ea26da077" --security-group default \
  --key-name mykey provider-instance
```





## ubuntu 

> openstack image create "cirros" \
>   --file cirros-0.3.4-x86_64-disk.img \
>   --disk-format qcow2 --container-format bare \
>   --public



```shell
wget http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.img # 下载

openstack image create "ubuntu_1804" \
  --file bionic-server-cloudimg-amd64.img \
  --disk-format qcow2 --container-format bare \
  --public
  
openstack flavor create --id 0 --vcpus 1 --ram 512 --disk 20 m1.small

```





## OpenStack报错:Host is not mapped to any cell

```
root@zy-openstack:~# nova-manage cell_v2 discover_hosts --verbose
Found 2 cell mappings.
Skipping cell0 since it does not contain hosts.
Getting computes from cell 'cell1': c833a5f0-612e-4e92-a68b-2460892a85bf
Checking host mapping for compute host 'zy-openstack': e11fcef8-9ca9-4a51-981b-59d333afca08
Creating host mapping for compute host 'zy-openstack': e11fcef8-9ca9-4a51-981b-59d333afca08
Found 1 unmapped computes in cell: c833a5f0-612e-4e92-a68b-2460892a85bf
```



## domain & project

域和项目

1. 每个域的项目和用户不能公用

```shell
# domain
openstack domain create testdomain
openstack domain list
openstack domain set testdomain --disable
openstack domain delete testdomain # 删除前必须先 distable

# project
openstack project create --domain testdomain testporject
openstack porject list
openstack project delete testdomain

# user
openstack 

# role
openstack add create admin # 创建角色
```



1. domain 更像是区域的概念，domain 所有账号都是独立的，可以同名。

2. project 是分组的概念。一个 domain 下的 project 可以被同一个 admin 管理。

3. 不同 domain 下的资源概念上无法互通。

4. 同一个 domain 下不同的 project 有些资源可能互通。比如共享 network， 同享的 image，还有 flavor。

   