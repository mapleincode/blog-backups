---
title: 升级 openSUSE Leap 15.3 到 Leap 15.4
tags: [opensuse, linux]
date: 2024-8-12 20:34:14
---

[openSUSE Leap 15.4](https://get.opensuse.org/leap/15.4/) 已经正式发布有些时候了，今天偶然去用一台给儿子玩的旧笔记本，发现还运行的是 openSUSE Leap 15.2，估计还是写「[从 openSUSE Leap 15.1 升级到 Leap 15.2](https://cnzhx.net/blog/upgrade-opensuse-leap-15-1-to-leap-15-2/)」那篇博文的时候升级的。先给[升级到了 15.3](https://cnzhx.net/blog/upgrade-opensuse-leap-15-2-to-leap-15-3/)，然后紧接着就又给升级到了 15.4。非常高兴，openSUSE Leap 的小版本升级过程越来越轻松了。

从 openSUSE Leap 15.3 到 Leap 15.4 的过程在 [openSUSE 的社区维基](https://en.opensuse.org/SDB:System_upgrade_to_Leap_15.4)有专门说明。虽然维基上也有[图形界面的升级过程](https://en.opensuse.org/YaST_Online_Update)，但是已经很久没有更新了。既然使用 Linux 还是多多尝试用命令行吧，很多操作都是比图形界面省事。下面的说明是在官方说明的基础上加了一些常见的操作。

**目录 Contents**

- [1 说明](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-1)
- [2 备份](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-2)
- [3 系统更新](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-3)
- [4 检查更新源](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-4)
- [5 更新要更新的软件源](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-5)
- [6 执行系统版本升级](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-6)
- [7 重启系统以完成更新](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-7)
- [8 更新：升级服务器遇到问题](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-8)
- [9 更新2：使用镜像源来更新系统](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-9)
- [10 更新3：重命名 repo 文件的文件名](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-10)

## 说明[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-1)

官方支持的**升级路径是从 openSUSE Leap 15.3 到 15.4**。所以如果当前使用的版本低于 15.3，还是像我开头描述的那样，一步步升级吧。虽然耗时长点，但是不容易出问题。

为了省事儿，下面的操作都是直接以 root 身份运行指令，所以**敲回车键之前千万要确认指令没问题**。

```
$~> su
```

## 备份[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-2)

备份数据一直都是重中之重。我也是有了很痛苦的经历之后才养成了至少两个异地备份才放心的习惯 :D

我在升级 [Linode VPS](https://cnzhx.net/go/linode) 上的主机时，都会做磁盘克隆。如果磁盘空间足够，就将当前磁盘做个克隆，以防万一。在 Linode 的云管理系统中，打开目标 VPS，并切换到 `Storage` 标签页，在对应的磁盘右边有三个小圆点，点一下就能看到 `Clone` 操作了。可以克隆到当前 Linode 也可以克隆到一个新的 Linode 中。不需要关机，且耗时极短。或者愿意的话，也可以使用 Linode 官方的备份功能，或者开个新的 Linode VPS。

## 系统更新[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-3)

先把 15.3 的系统更新到最新状态，

```
# zypper refresh && zypper update
```

如果又更新了一大堆东西，那就重启一下系统再往下继续。

## 检查更新源[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-4)

如果系统中使用了除官方软件源之外的其它软件源，如 packman 之类的，需要先禁用它们，等系统更新之后再重新启用或者添加适合新系统的其它软件源。

查看系统中当前启用的软件源，

```
# zypper repos --uri --show-enabled-only
# | Alias                             | Name            | Enabled | GPG Check | Refresh | URI 
--+-----------------------------------+-----------------+---------+-----------+---------+------------------------------------------------------------------ 
1 | Leap-${releasever}-Non-OSS        | Leap-${releas-> | Yes     | (r ) Yes  | No      | http://download.opensuse.org/distribution/leap/15.3/repo/non-oss/ 
2 | Leap-${releasever}-OSS            | Leap-${releas-> | Yes     | (r ) Yes  | No      | http://download.opensuse.org/distribution/leap/15.3/repo/oss/ 
3 | Leap-${releasever}-Update-Non-OSS | Leap-${releas-> | Yes     | (r ) Yes  | No      | http://download.opensuse.org/update/leap/15.3/non-oss/ 
4 | Leap-${releasever}-Update-OSS     | Leap-${releas-> | Yes     | (r ) Yes  | No      | http://download.opensuse.org/update/leap/15.3/oss/
5 | repo-backports-update             | Update reposi-> | Yes     | (r ) Yes  | Yes     | http://download.opensuse.org/update/leap/15.3/backports/ 
6 | repo-sle-update                   | Update reposi-> | Yes     | (r ) Yes  | Yes     | http://download.opensuse.org/update/leap/15.3/sle/
```

注意看我将 URI 那一列标红的三个，它们必须得有，且为启用状态。也就是其对应的 `Enabled` 那一列中显示为 `Yes`，那就没问题了。当然如果没有的话，或者没有启用的话，就需要添加并启用。如果没看到这三个，或者不全，可以看看是不是没启用。用下面这个指令看看，

```
# zypper repos --uri
```

如果实在不行了，自己添加一下也是一样的，

```
# zypper addrepo --check --refresh --name 'repo-update' http://download.opensuse.org/update/leap/15.3/oss/ repo-update
# zypper addrepo --check --refresh --name 'repo-backports-update' http://download.opensuse.org/update/leap/15.3/backports/ repo-backports-update
# zypper addrepo --check --refresh --name 'repo-sle-update' http://download.opensuse.org/update/leap/15.3/sle/ repo-sle-update
```

另外，应该大家都注意到了表里面列出来的还有变量 `${releasever}`。这个可就是我们在[从 15.2 升级到 15.3](https://cnzhx.net/blog/upgrade-opensuse-leap-15-2-to-leap-15-3/) 的时候加进去的，方便升级用的。下面会直接给 `releasever`赋值来换新版本的软件源而不需要手动修改软件源地址了。

再一个，如果没看到变量 `${releasever}`，而是看到了下面这样的 `openSUSE-Leap-15.3-Update-Non-Oss`（表格借用自[这里](https://www.cyberciti.biz/faq/how-to-upgrade-opensuse-using-the-cli/)），那就说明需要手动替换一下，使用[从 15.2 升级到 15.3](https://cnzhx.net/blog/upgrade-opensuse-leap-15-2-to-leap-15-3/) 的第 3 点里面那条指令，只是需要将其中的 15.2 改成 15.3。

```
# | Alias                             | Name                                                         | Enabled | GPG Check | Refresh | URI
--+-----------------------------------+--------------------------------------------------------------+---------+-----------+---------+------------------------------------------------------------
1 | openSUSE-Leap-15.3                | openSUSE-Leap-15.3                                           | Yes     | (r ) Yes  | Yes | http://download.opensuse.org/distribution/leap/15.3/repo/oss/
2 | openSUSE-Leap-15.3-Update         | openSUSE-Leap-15.3-Update                                    | Yes     | (r ) Yes  | Yes | http://download.opensuse.org/update/leap/15.3/oss/
3 | openSUSE-Leap-15.3-Update-Non-Oss | openSUSE-Leap-15.3-Update-Non-Oss                            | Yes     | (r ) Yes  | Yes | http://download.opensuse.org/update/leap/15.3/non-oss/
4 | repo-backports-update             | Update repository of openSUSE Backports                      | Yes     | (r ) Yes  | Yes | http://download.opensuse.org/update/leap/15.3/backports/
5 | repo-sle-update                   | Update repository with updates from SUSE Linux Enterprise 15 | Yes     | (r ) Yes  | Yes | http://download.opensuse.org/update/leap/15.3/sle/
```

把其中除了上表中这些之外的其它源都禁用掉。可以按照前面 # 那一列的序号来禁用。

## 更新要更新的软件源[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-5)

```
# zypper --releasever=15.4 ref
```

## **执行系统版本升级**[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-6)

提前下载所有更新包，允许切换 vendor（因为这中间有不少软件包都换了来源了），并更新，

```
# zypper --releasever=15.4 dup --download-in-advance --allow-vendor-change
............
The following product is going to be upgraded:
openSUSE Leap 15.3 15.3-2 -> 15.4-1
............
The following 5 packages are going to change architecture:
ModemManager-bash-completion x86_64 -> noarch
plasma5-session-wayland noarch -> x86_64
plymouth-dracut x86_64 -> noarch
plymouth-scripts x86_64 -> noarch
xorg-x11-libX11-ccache noarch -> x86_64
............
1636 packages to upgrade, 29 to downgrade, 170 new, 47 to remove, 5 to change arch.
Overall download size: 2.00 GiB. Already cached: 0 B. After the operation, additional 165.5 MiB will be
used.
............
```

## 重启系统以完成更新[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-7)

```
# reboot
```

如果有来自其它软件源的软件，重启之后就可以重新起用/添加这些软件源并更新了，

```
# zypper repos -d
# zypper mr --enable ${x}
```

其中 `${x}` 就是需要重新启用的软件源的序号。

另外，重启之后最好再执行一下系统更新，

```
# zypper refresh && zypper update
```

如果是桌面系统，一般都会安装 packman 软件源来使用多媒体相关的软件包。假设 packman 源的序号为 `${packman_num}`。 先启用 packman 源，

```
zypper mr -e ${packman_num}
```

然后从该源执行升级，

```
zypper update -r ${packman_num} --allow-vendor-change
```

升级过程非常顺利。我目前只升级了那台旧的笔记本，服务器还没有升级。虽然整个过程看起来非常顺畅了，可是不怕万一嘛，还是等我有空了再升级吧。

## 更新：升级服务器遇到问题[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-8)

昨晚上批改作业，简单的重复劳动。我寻思着既然升级过程这么丝滑，不如一边批改作业一边升级一下家用的服务器吧。然后就悲剧了。简单来讲，就是按照上面的步骤，升级过程依然**比较**丝滑，最后发现服务器上的**PHP网页服务无法访问**了。

首先遇到的一点小问题就是升级冲突，有两个问题，如下。

```
Computing distribution upgrade...
2 Problems:
Problem: the to be installed tuned-2.10.0-150400.19.10.noarch requires 'util-linux', but this requirement cannot be provided
Problem: the installed kernel-preempt-extra-5.3.18-150300.59.98.1.x86_64 requires 'kernel-preempt_x86_64 = 5.3.18-150300.59.98', but this requirement cannot be provided
```

这倒是新鲜哈。尝试了一番，感觉选择删除 `kernel-preempt-*` 开头的那几个软件包就可以解决问题。所以在推荐的解决方案中都选择了下面的 2。

```
Solution 2: Following actions will be done:
deinstallation of kernel-preempt-5.3.18-150300.59.93.1.x86_64
deinstallation of kernel-preempt-5.3.18-150300.59.98.1.x86_64
deinstallation of kernel-preempt-extra-5.3.18-150300.59.98.1.x86_64
deinstallation of kernel-preempt-optional-5.3.18-150300.59.93.1.x86_64
deinstallation of kernel-preempt-optional-5.3.18-150300.59.98.1.x86_64

Solution 2: Following actions will be done:
deinstallation of kernel-preempt-extra-5.3.18-150300.59.93.1.x86_64
deinstallation of kernel-preempt-extra-5.3.18-150300.59.98.1.x86_64
deinstallation of kernel-preempt-optional-5.3.18-150300.59.93.1.x86_64
deinstallation of kernel-preempt-optional-5.3.18-150300.59.98.1.x86_64
deinstallation of kernel-preempt-5.3.18-150300.59.98.1.x86_64
```

没错，决定删除 `kernel-preempt-*` 之后，就发现这两个问题其实都是因为 `kernel-preempt-*` 软件包，所以推荐的解决途径也是一样的策略。

我也不记得什么时候安装的 `kernel-preempt-*`[ 内核](https://software.opensuse.org/package/kernel-preempt)。想想官方的 openSUSE Leap 应该不会推荐安装 preempt 的内核吧。可能是我啥时候心血来潮试了试。然后搜索的时候发现还真有[可能是系统更新的时候 openSUSE 自动给我安装的](https://forums.opensuse.org/showthread.php/548018-Kernel-preempt)。

```
The following 244 NEW packages are going to be installed:
......... kernel-default-5.14.21-150400.24.28.1 .........virtualbox-kmp-default-.........

The following 63 packages are going to be REMOVED:
```

检查了一下，没有啥是我明确需要又被删除的。其中就包括 `kernel-preempt-*` 的一大堆以及 `virtualbox-kmp-default-*` 和 `virtualbox-kmp-preempt-*` 的一大堆。然后它们的其它版本都包含即将新安装的 244 个包中。

```
1338 packages to upgrade, 37 to downgrade, 244 new, 63 to remove, 23 to change vendor, 4 to change
arch.
Overall download size: 1.32 GiB. Already cached: 0 B. After the operation, 234.0 MiB will be freed.
```

所以应该没问题，就输入 y 执行安装了。大约 50 分钟后安装完毕，重启，常用服务都正常工作。

再晚点就发现问题了，nextcloud 所在的 [LAMP+PHP-FPM 服务](https://cnzhx.net/blog/setup-apache-mariadb-php-php_fpm-lamp-opensuse-leap-vps/)在访问的时候始终都是 403 错误。折腾了个多小时也没处理好。目前可以确定的是 Apache 可以正常工作，PHP-FPM 在解析 php 文件的时候报错。更新：[已确认是 AppArmor 的限制](https://cnzhx.net/blog/php-access-denied-after-upgrading-opensuse-leap-from-15-3-to-15-4/)，并不是升级过程的问题。

## 更新2：使用镜像源来更新系统[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-9)

详见[新博文](https://cnzhx.net/blog/update-opensuse-from-mirrorcache/)。使用 mirrorcache.o.o 而不是 download.o.o 来更新系统速度往往会更快，而且也可以减轻主服务器 download.o.o 的压力。

## 更新3：重命名 repo 文件的文件名[¶](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/#i-10)

上面的更新过程并不会修改 `/etc/zypp/repos.d/` 目录中放置源配置的文件的文件名。虽然系统升级到 15.4 了，而且源中的配置也使用变量`${releasever}`了，但是文件名可能还是包含 15.3 甚至 15.2 字样的。这些文件名是可以直接修改的。例如，

```
cd /etc/zypp/repos.d
mv Leap-15.2-OSS.repo repo-oss.repo
mv Leap-15.2-Non-OSS.repo repo-non-oss.repo
mv Leap-15.2-Update-OSS.repo repo-update-oss.repo
mv Leap-15.2-Update-Non-OSS.repo repo-update-non-oss.repo
```

这样修改并不会有其他影响，`zypper lr -dE` 看到的内容是不变的。[©](https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/)

本文发表于[水景一页](https://cnzhx.net/)。永久链接：<https://cnzhx.net/blog/upgrade-opensuse-leap-15-3-to-leap-15-4/>。转载请保留此信息及相应链接。