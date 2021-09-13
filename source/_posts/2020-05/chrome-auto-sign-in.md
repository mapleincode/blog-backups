---
title: 解决 Chrome 无法自动填充账号密码的问题
tags: [ chrome ]
date: 2020-05-18 09:46:08
---

前段时间在折腾浏览器的时候，不知道怎么一搞，导致在登录界面，Chrome 不会自动把账户密码填充到登录账户密码栏里。也就是进入一个页面之后，需要手动点击账户栏，选择账户才会填入正确密码。

我尝试重新登录账户，清除数据，卸载 Chrome 重启等操作均无法解决问题。

忽然想到，MacOS 拖程序到回收站其实并不算完全卸载，即使删除用户数据，也会存留部分数据在用户数据文件夹。

> 一般情况我会使用 CleanMyMac 来卸载，没想到这玩意儿也没有完全卸载。

搜索 Google （吐槽重置之后，地址栏输入居然变成了百度）之后，找到了卸载 Chrome 的正确姿势：

1. 在计算机屏幕的底部，右键点击 Dock 中的 Chrome。
2. 选择**退出**。
3. 打开 Finder。
4. 在您的计算机上转到包含 Google Chrome 应用的文件夹。
   - **提示**：该应用可能会位于您的“应用程序”文件夹中。如果没有，请依次转到**文件** ![接着点击](https://lh3.googleusercontent.com/SaY5lqCwN7kppnS546l9ys-E2sZftTTIHjBrdV-WsGPIhGjaxcEXjfgdIfW_UNG7Sw0=w13-h18) **查找**，然后搜索“Google Chrome”。
5. 将 Google Chrome 拖到“废纸篓”中。
6. 可选：删除您的个人资料信息（例如书签和历史记录）：
   1. 在屏幕顶部，依次点击**前往 ![接着点击](https://lh3.googleusercontent.com/SaY5lqCwN7kppnS546l9ys-E2sZftTTIHjBrdV-WsGPIhGjaxcEXjfgdIfW_UNG7Sw0=w13-h18) 前往文件夹**。
   2. 输入 `~/Library/Application Support/Google/Chrome`。
   3. 点击**前往**。
   4. 选择所有文件夹，然后将其拖到“废纸篓”中。



因为我们只需要重置浏览器，其实都没必要卸载，直接：

```sh
rm -rf ~/Library/Application Support/Google/Chrome
```



重新打开浏览器，提问选择是否设置为默认浏览器。重新尝试是否可以自动填充密码，成功。