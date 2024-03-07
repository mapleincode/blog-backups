---
title: 通过 docker 来部署 outline
date: 2024-02-22 11:26:19
tags: [outline]
---

同样是写 traefik 的老哥，他也写了 outline 的教程：

[从零开始使用开源文档/Wiki软件 Outline（一）](https://soulteary.com/2021/09/05/opensource-documentation-wiki-software-outline-part-1.html)



需要注意的是，整个项目的部署是基于 traefik 来进行部署的，除此之外教程相当详细。



## 为什么要使用域名？

域名可以保证使用时候比较舒服，因为所有服务都可以使用同一个 80 端口或者 443 端口，而不需要每个服务单独占用一个独立的非标准端口。



这样的情况下，http 服务器区分服务的办法，只能以来域名。



并非一定需要购买域名，例如范例中的 `*.lab.com`，只需要在 hosts 文件中加入映射即可访问。



## 是否一定要使用 https

https 取决于你是否在 traefik 里配置了 https，如果配置了，直接是使用作者的 https 配置就可以了，反之，可以参考我的 traefik 教程进行修改，将所有的服务的 https 改成 http，删除相关的 https 配置即可。



外网使用推荐使用 https，以免造成信息泄露。



## 是否可以使用自己原生部署的 Redis 和 Postgres

可以。



## 上传附件和图片返回无权限？

截图忘记了，大概率是没有在 minio 上创建 Bucket。

登录 minio 的管理端，在左侧 ADMIN - Buckets 创建一个 outline 的 Bucket。（创建按钮在右上角，创建配置默认即可）。



## linx-server docker permission denied



linx-server 作者是作为一个附件服务器。

> 虽然好奇为什么不直接把 minio 作为附件服务器呢？明明也是对象存储。



直接部署使用会出现权限问题，登录 linx-server 发现用户是 nobody。

但是使用 chown 无法使用 nobody 的用户进行授权。

最简单的办法，登录 linx-sever 的系统，然后输入 id ，返回当前用户的 id，再在宿主机上对无权限的文件夹进行 chmod 授权即可。

> 后面发现直接授权 chown nobody:nogroup data 也应该是可以的



参考：

https://tech-blog.rakus.co.jp/entry/20200826/docker