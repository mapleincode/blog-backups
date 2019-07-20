---
title: Let's encrypt 证书申请
date: 2019-07-21 03:17:32
---

呃，这大概是我最短的一次证书申请，比我私签的证书简单了上百倍，全程就花了 1 分钟左右。



## 申请证书

直接贴地址：

[https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E](https://github.com/Neilpang/acme.sh/wiki/说明)

国人的作品，我无法表达我的感受，能把一件这么复杂的事情做的和电脑登录微信这么简单。



首先是安装

```sh
curl  https://get.acme.sh | sh
```



然后目录位置是

```
~/.acme.sh/
```



验证域名有两个方式

1. 在网站配置一个静态文件
2. 在 DNS 配置一个 TEXT 类型的域名文件



主要都是为了验证你就是域名所有人。

第一种方法据说不能适配通用域名，也就是 `*.domain.com`。而对于我这种多子域名的人来说有点不合适。所以采用第二种方式。



申请证书:

```sh
acme.sh  --issue  --dns   -d mydomain.com
```

更新证书:

```
acme.sh  --renew   -d mydomain.com
```



由于涉及 dns 修改，所以他提供了相关 dns 服务商的 api 修改的账号配置入口。

例如我用的就是范例中的 `dnspod.cn`

```sh
export DP_Id="1234"

export DP_Key="sADDsdasdgdsf"

acme.sh   --issue   --dns dns_dp   -d aa.com  -d www.aa.com
```

之后生成的话也是自动化:



```sh
acme.sh  --issue   -d  mydomain2.com   --dns  dns_dp
```



然后配置证书

```sh
acme.sh  --installcert  -d  <domain>.com   \
        --key-file   /etc/nginx/ssl/<domain>.key \
        --fullchain-file /etc/nginx/ssl/fullchain.cer \
        --reloadcmd  "service nginx force-reload"
```



## 关于最后

这个大概是我目前遇到过最良心的脚本涉及，文档简单易懂，参数简单，考虑多种情况。

除了 dns 验证支持 dns 服务商之外，对于静态文件的验证也支持了 nginx 、apache。

我觉得今后，有关脚本，无论是否有人会用，至少得考虑的像他一样完善，不仅仅因为，觉得麻烦、反正没人用、反正是免费的别要求这么高等情况偷懒。



反观 Ant.d 之前"彩蛋时间"，不少高级程序员冷嘲热讽相对比，这才是我想学习的开源精神。