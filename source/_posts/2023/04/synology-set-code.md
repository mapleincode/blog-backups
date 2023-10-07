---
title: 黑群晖新引导洗白（转载）
date: 2023-04-04 18:59:54
tags: [synology]
---



[原文](https://razeen.me/posts/nas-06-syno-hack/)

在前面的文章中，我们利用ARPL引导将黑群晖安装好了。有朋友就问我，ARPL安装的黑群晖洗白和其他的不一样了，该怎么洗白？这篇文章为你解答。



### 原理

其实洗白的主要就是修改引导里面的 sn 和 mac 地址。 我们只需要找到引导里面的 sn 和 mac 地址就好了。 而 APRL 引导中， sn 和 mac 地址都在 user-config.yml 中。 也就是说，修改这个文件就好了。

### 操作

知道了原理，操作就简单了。

第一步，我们需要开启群晖的SSH，这一步相信大家都很熟悉，这里就不累述。

第二步， SSH 连接上群晖。

第三步，`sudo -i` 切换到 root 权限下。

第四步，创建临时目录，用于挂载引导分区。

```
mkdir /tmp/synoboot
```

第五步，挂载引导分区。

```
cd /dev
mount -t vfat synoboot1 /tmp/synoboot
```

第六步，修改 user-config.yml 文件，修改好后保存。

（vi 操作， i 进入编辑模式, 上下左右移动光标，ESC 退出编辑模式，:wq 保存并退出）

```
cd /tmp/synoboot/
vi user-config.yml
```

第七步，重新启动。

这就洗白成功了。

最后提示一下，7.0以上的系统，洗白账号容易被封，谨慎操作。