---
date: 2020-05-02 00:41:01
title: 群晖 Drive 反复同步问题
tags: [ Synology ]
---

群晖作为新手入门必选 NAS，有着丰富的 NAS 功能和软件支持。是认为比较成熟的闭源软件。

> 但是我作为一个穷逼，实在不敢向老婆要四千块钱去买个 418，去年的网络蚂蚁的矿车很香！
>
> 推荐大家有条件还是去上正版群晖。



Drive 是从群晖 6.x 推出的新概念，相当于是作为一个云端的文件存储器，有配套的软件实现文件同步。类似国内软件坚果云。



因为有在家里办公的习惯，所以把公司的代码和文件统统同步到家里的 NAS，然后在同步到家里的 MAC 电脑。

> 虽然已经被裁员了 Q^Q

Drive 提供实时文件传输，加上网络条件比较合适，基本可以实现实时文件修改。甚至可以实现跨系统的文件支持。



在 Windows 倒没有出现问题，但是我在公司是 Mac，在家里也是 Mac。经常出现文件反复同步的文件。

经过反复的尝试，确认以下几天:

1. 无论是否开启 “高级一致性检测” 都不能解决问题
2. 最主要原因还是缩略图，除了常见的图片文件，因为安装了 xcode，会对代码文件也生成缩略图，也会导致这个问题发生。
3. zip 压缩文件也存在这个问题，原因不知。

**猜测原因是，一旦 Mac 为了图片生成缩略图，会导致触发某个钩子，而 Drive 检测文件是否被修改，就是使用了这个钩子（实际上文件只是生成了缩略图，文件本身没有被修改，可能被修改了某个基于系统的属性）。**

解决办法有以下几种措施：

1. 避免两个 Mac 客户端同时在线，这是解决这个 BUG 最终极的办法，一般人应该都可以满足这个条件，例如把 Mac 关屏就不会有这个问题。

   > 对大部人来说都没有问题，对我来说就不是解决办法，符合以下条件的同学请继续网线看
   >
   > - 用的是 mac mini or imac 而不是 macbook
   > - 不习惯关屏
   > - 不设置系统自动休眠

2. 因为缩略图是万恶之源，只能消灭缩略图。首先是删除所有图片，因为图片都会造成生成缩略图。其次，把文件的显示模式改成了文件列表，而不是图标。这个可以避免文件类生成缩略图。