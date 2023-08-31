---
title: 强关 esxi，数据损坏和修复
date: 2023-07-26 15:51:05
tags: [esxi, synology, ubunutu, gitlab]
---



## 起因

我家里用了一台 x79 平台作为 Esxi。这台机器有一个不算毛病的毛病，就是在大量传输文件的时候，会出现网口堵塞，也就是机器不死机，但是网络却无法访问。

前几天刚好在备份数据，网络却忽然不通了。因为我人在公司，于是我习惯性的使用米家 APP 去强关机器并重启。



## 黑群辉修复

我在虚拟机整了一个黑群晖，使用两块机械硬盘和一块虚拟盘组成。因为虚拟盘是基于 SSD，所以相对速度较快，我一般作为主盘，用于安装套件。

进入群晖之后，显示盘损毁。因为群晖每个分区都会安装系统文件。因为这个原因，即使只剩下一个任意一个盘，也能正常开机。

尝试通过页面和指令进行修复。修复失败。



因为群晖的 Basic 模式，其实本质也是基于软阵列的方式创建。所以在数据丢失比较严重的情况，同样无法修复。

> 这里要劝诫，如果资金允许，务必不要使用 Basic 模式。一旦硬盘因为意外丢失数据，则整个盘的数据都会化为虚无。
>
> 如果要使用，则务必做好备份。

> 当然我这次损坏，很明显是因为 ssd 缓存导致的，一旦在断电的时候较大功率写入，很容易导致缓存里的数据丢失。而机械盘在保存数据安全性远比 ssd 要来得好，



确定无法恢复数据，那只能抛弃这些应用数据，并且重新创建一个数据盘了。

这次我干脆创建了两块虚拟盘，并作为 raid1 的方式来运行。



这里还有插入一个点是：

因为我使用的是 6.1 版本的黑群晖，因为官方已经停止维护 6.1 版本，所以很多套件安装会出现问题。建议去

https://archive.synology.com/download/Package

查找合适的老版本进行安装。



## Ubuntu 修复只读

本以为事情就这样过去了，没想到只是刚开始。

第二天，在我推送代码的时候，我发现 Gitlab 挂了。

登录虚拟机的时候，发现虚机出于只读状态。

这时候通过 fsck 直接修复

```shell
fsck -Af -y
```



修复完成之后，重启就可以正常系统。

但是很明显，很多文件都被损坏了，导致使用 apt upgrade 会出现一堆报错。

```
dpkg: warning: files list file for package 'libgcc-s1:amd64' missing; assuming package has no files currently installed
```



一开始和网上说的一样，使用 `--fix-missing` 参数实现强制更新，发现无效

```
sudo apt upgrade --fix-missing
```



或者使用 `--reinstall` 命令对缺失文件的包进行强行重装也依然无效。

---

通过多次尝试，我发现无法修复的原因是，apt 在安装依赖之前，先会校验已安装的包的依赖状况。以至于在包不完整的情况下，上机依赖下级包会出现抛错。

这时候，我想起有人提到过一点，可以把`/var/lkib/dpkg/info/` 目录下的不正常的包先备份，然后重新安装。虽然在移除了缺失的包之后，因为上级依赖问题，依然会出现缺失包的问题。但如果，我把所有依赖的上级包全部删除，是否就可以不影响而且可以正常使用？

于是，在我移除了 n 个包之后，apt upgrade 终于可以正常运行了

```shell
for package in $(apt-get install --reinstall x 2>&1 |\
             grep "warning: files list file for package '" |\
             grep -Po "[^'\n ]+'" | grep -Po "[^']+"); do
    apt-get install --reinstall "$package";
done
```

使用上述脚本之后，apt 就自动修复了所有缺失的依赖。



## Gitlab 修复

系统文件都出现丢失，那 gitlab 就更不能避免。于是乎，Gitlab 启动不了，查看了 gitlab-rails 的日志，发现提示 ruby 无法 load `cgi/util` 模块。



因为对 ruby 一窍不通，所以一开始盲目的在 Google 百度进行查询，发现几乎没有相同的问题。当时想的是会不会是缺失依赖。结果忙乎了好几个小时都毫无收获。

这时候我发现了网上有一个相关的搜索结果:

``` 
/opt/gitlab/embedded/lib/ruby/2.3.0/cgi/util.rb
```



莫非，cgi/util 是 ruby 自带的依赖？

我查看了 gitlab 自带的 3.0 版本的 ruby，发现的确不包含这个依赖。会不会是 Gitlab 升级了 ruby 版本，但是代码依然是 2.x，而 3.x 移除了这个依赖？百思不得其解。知道我发现 Ubuntu 系统居然也带了一个 ruby 版本，并且刚好是 3.0.7（gitlab 带的是 3.0.0）。

查阅了相关文件，发现 ruby 3.0 版本的确还是有 `cgi/util` 模块，而系统修复刚好把整个文件夹都删除了。

于是把自带的 ruby 的 cgi 文件夹复制进入 gitlab ruby 并重启系统，gitlab 终于可以正常启动了。



然而，这才是开始

---

gitlab 系统都出现损坏，那 gitlab 的仓库就更不用说。虽然登录 gitlab 的时候感动的要掉泪，但是一刷页面，就立刻开始循环 500 了。

万幸的是，gitlab 自带的数据局 postgresql 并没有出现任何问题。

总结下，一共会出现 4 类问题：

1. repo 文件丢失，提示重新建 repo
2. repo 虽然可以进到详情页，但是详情页里没人任何文件，并且在分支切换提示错误
3. repo 页面直接  500
4. 只要涉及这个 repo 的 repo 列表，一律 500。



相对于 3,4, 1 和 2 属于比较好解决的，直接删掉仓库重建，并且重新推送代码就能正常。

而 3，4 则有相当大的麻烦。

首先我不知道怎么才能在不进入页面的情况下去删除一个 repo。3 的情况，至少保证可以在 admin 页面查看 repo 的 id。而 gitlab 的 repo 文件存储，是基于 id 的 hash。

所有文件都存与 `/var/opt/gitlab/git-data/@hashed` 下，而真实地址，则由数字的 repo id 通过哈希计算得出。

```shell
echo -n 60 | sha256sum
```

例如  id = 60 的仓库，计算 sha256 结果是 `39fa9ec190eee7b6f4dff1100d6343e10918d044c75eac8f9e9a2596173f80c9`

则文件最终目录为 `/var/opt/gitlab/git-data/@hashed/39/fa`。



但是 4 的情况，我连仓库的 id 都看不到。



---

这时候我发现，有人推荐使用设置数据库里的 projects 表的参数进行屏蔽 project，我测试了参数并没有什么用。但是我死马当活马医，直接把对应的表数据进行删除，发现 repo 居然直接变成 404，并且页面也不在报错。

于是搞了一晚上的，删 repo，重建 repo， 重新 push。

这时候，我发现依然还缺失部分 repo，而且刚好本地和 gitlab 都没有。

这时候我想起，还有一个 2020 年的 gitlab 备份。



## Gitlab 的备份和恢复



gitlab 的备份相当简单：

```shell 
sudo gitlab-rake gitlab:backup:create
```



备份完成之后，备份文件会被保存在 `/var/opt/gitlab/backup` 里，而恢复备份，则是

```shell
# 关闭数据库相关进程，其实不关也是可以的，但是如果是多人使用就必须关闭，以免出现数据不一致的情况
sudo gitlab-ctl stop unicorn
sudo gitlab-ctl stop sidekiq

# 12 版本的恢复，需要传入的是 时间戳 + 版本 id 部分
# 而最新的 16 版本，则要求传入完整的备份名称
sudo gitlab-rake gitlab:backup:restore BACKUP = 1521884424_2018_03_24_10.5.3
```



一开始我尝试在新版本，对这个 3 年前的备份进行恢复，因为我是虚拟机，只要开启快照就能避免数据丢失。

但是报错，提示必须使用对应的版本...



那行吧，重新开一台老版本来安装虚拟机吧。

我选用的 gitlab 文件是：

```
https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/bionic/gitlab-ce_12.9.2-ce.0_amd64.deb

wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/bionic/gitlab-ce_12.9.2-ce.0_amd64.deb/download.deb
```



因此需要安装 bionic 的系统 18.04 ，下载 deb 文件后 ，使用 `dpkg -i *.deb`进行安装。

之后就像是安装 gitlab 一样，设置 url 之后再 `gitlab-ctl reconfigure`。就行正常访问 gitlab 服务器。



需要注意的是，恢复备份之后，账号都被重置并且无法登录。

首先我们需要重置 root 密码，首先进去 gitlab-rails 管理端：

```shell
gitlab-rails console -e production
```



然后通过相关操作来重置 root 密码 

```
user.password = '新密码'
user.password_confirmation = '新密码'
user.save
```

如果控制台返回 true，则说明重置成功。



> 登录 gitlab 之后，可以把以前的普通账号解冻（默认都是冻结），这样就可以正常登录以前的账号。

到此为止，恢复备份成功。



# 教训和经验

这次出现多个虚拟机文件丢失，很大程度有几个原因：

1. 自己强行关机
2. 采用家用 ssd，以至于再断电的情况下无法保存数据
3. ssd 本身使用强度较高，可能寿命已经快要结束



首先，我去思考了关于 esxi 断网的问题，想到，既然重新插拔网线就能重置网络，那我就可以在交换机上加个开关。通过控制交换机的重启来实现重置网络的问题。这比重置 ESXi 要便捷并且安全的多。

其次是，重要的数据需要做好安全防护。这次事故，让我几十 G 的数据库免于的灾难，很大程度上就是我很早前就把相关的虚拟机迁移到一个拥有超多电容的服务器 SSD 上。

更其次，需要做好定期备份的问题。











