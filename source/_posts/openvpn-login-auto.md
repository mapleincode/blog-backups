---
title: 使用 Expect 自动登录 OpenVPN
date: 2018-02-22 17:23:19
tags: [ Expect, Linux, OpenVPN ]
---

公司使用 OpenVPN 作为外网办公网络，本来只设置了用户名密码，但是为了安全，公司的网(yun)管(wei)同事升级了安全配置，添加了谷歌验证的输入需求。



谷歌验证是一种将预先生成秘钥和时间戳，通过一种开源算法计算出若干位的数字的安全方案。在 VPN 配置界面，会提供一个二维码(即为秘钥)，然后在手机上安装 APP，扫码二维码并保存，可以获得一个动态刷新的验证码。然后在登录界面输入账号密码和动态验证码即可登录 VPN。



作为一个电脑党，每次登录 VPN 都需要满世界找手机，真的让人觉得麻烦。因为谷歌验证是开源的，所以 gayhub 上到处都有各个编程语言支持的包。如果可以运行一套程序，自动获得验证码并填入 OpenVPN 的客户端，既可以一键登录 VPN，岂不是美滋滋。



## 获得动态验证码

首先要获得秘钥，一般 OpenVPN 配置表那边都会显示一串英文字符串，复制下来，那个就是秘钥。有的只有二维码，也不麻烦，手机找个二维码扫码的 APP，直接扫码二维码，就可以获得那串字符串。

注意，这个秘钥相当于你的密码，请妥善保存。一旦被第三者获得，就可以利用你的秘钥为所欲为。

作为 Node 🐶，当然选择 JavaScript 的算法包。写了一个简单的脚本。

```javascript
#!/usr/bin/env node
"use strict";

const code = "yoursecretcode"; // 秘钥
const GoogleAuth = require("google_authenticator").authenticator;
const nya = new GoogleAuth();
console.log(nya.getCode(code));
```

这里我选择直接输出，只要原因是 Expect。



## 自动填写账号密码

因为我对 Linux Shell 操作和原理都不是很熟悉。本来想采用 Node 的方法来调取 OpenVPN Client。后来发现这个方案属于牛头不对马嘴。

查询下某度(原谅我都是查完某度再去查某歌，不合格的👨‍💻‍)，提供了一个使用 Expect 的方案。

‘关于 Expect 具体使用的教程，我找了一个，但是因为格式问题，还在慢慢整理，等整理完了放出来。

```tcl
#! /usr/bin/expect
set timeout 30 # 设置超时时间
spawn openvpn --config client.ovpn # 启动 OpenVPN Client
set code [exec google-code/index.js] # 设置一个 code 变量，值就是我的 Node 脚本输出的值
expect "Enter Auth Username:" # 等待这个输出
send "username\r" # 模拟键盘输入 username
expect "Enter Auth Password:" # 等待这个输出
send "password\r" # 模拟键盘输入 password
expect "CHALLENGE: Enter Google Authenticator Code" # 等待这个输出
send "$code\r" # 模拟键盘输入 code 
interact # 将控制台打印移交给 openvpn 的进程
```

这边要注意 `send` 命令在内容后面需要加一个`\r`，模拟键盘的回车。

然后运行脚本，就可以自动输入账号信息来登录 OpenVPN。



## 最后 

Expect 的玩法不仅仅这些，还有更多，当时折腾到凌晨四五点(走了很多歪路，还好媳妇儿不在)，到最后可以搞定，已经是心满意足。Linux 上的自动化工具的确好用，以后还可以努力多尝试一个事情，让自动化来解决自己的麻烦事。