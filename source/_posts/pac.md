---
title: 自动科学上网 & PAC 
date: 2018-02-07 16:58:56
tags: [PAC]
---



>   一直很喜欢 Chrome 的书签和记录同步，也不喜欢第三方的来搞。但是因为我们伟大的长城，所以需要科学上网来实现数据同步。之前是租了一台 VPS，然后自己搭建 SS 服务器，无奈费用实在是太高。后来在同事的推荐下开了一个 SS 服务，16 刀一年，50G 一个月（P.S. 后面发现这个网站的流量是按照节点算的，慢的节点算 0.2，相当于 250G，对我这种之翻墙看看网页的乖巧少年来说完全够用了）。但是问题来了，iPad 的 SS 客户端我只能找到一个简单的小工具，且只支持`aes-256-cfb"`，但是 SS 服务商强制要求 `chacha20-ietf-poly1305`这种奇怪的加密方式，ORZ。



后面想到使用自动代理的方式，通过一个 PAC 文件，实现对网站的自动代理。而 SOCK5 则通过 ss 软件分享到局域网。通过 SOCK5 和 PAC 文件实现科学上网的自动代理。

还有一点是，我们公司使用的 VPN 为 openvpn。为了安全，设置了 Google 身份验证器。虽然对安全来说这是必要的，但是像我们日常只在一个网络中使用，偶尔看下`Gitlab`或者文档什么的，每次开 VPN 都需要到处找手机慢慢输入，而且 openvpn 的作用是全局，如果只是中途看一个网页就十分不方便。因此，我想把 openvpn 也挂载到一个服务器上，再在上面开启一个 SS 服务来实现用 SS 代理 openvpn 的环境。



## SS client & server

### 搭建

```bash
apt-get install python-pip
pip install shadowsocks
```



### 启动 ss 服务器

```sh
ssserver -p 8000 -k password -m rc4-md5
```

`-p`: port

`-k`:  password

`-m`: 加密算法

`-d`: 后台运行

### 启动 ss 客户端

```Sh
sslocal -c shadowsocks.json -d start
```

`-c`: config 文件

`-d`: 后台运行



### JSON config

ssserver 和 sslocal 其实都可以使用 `-c`参数来使用 JSON 文件。

```json
{
  "server":"0.0.0.0", 
  "server_port":8888, 
  "local_port":1080, 
  "password":"f36hh0eee0abad", 
  "timeout":600, 
  "method":"rc4-md5" 
}
```

## PAC

> 一个PAC文件包含一个[JavaScript](https://baike.baidu.com/item/JavaScript)形式的函数`FindProxyForURL(url, host)`。这个函数返回一个包含一个或多个访问规则的字符串。用户代理根据这些规则适用一个特定的代理器或者直接访问。 当一个代理服务器无法响应的时候，多个访问规则提供了其他的后备访问方法。 浏览器在访问其他页面以前，首先访问这个PAC文件。PAC文件中的URL可能是手工配置的，也可能是是通过网页的网络代理自发现协议（Web Proxy Autodiscovery Protocol）自动配置的。

拿一个网上找来的简单的例子:

```js
var domains = {
    "google.com": 1,
    "facebook.com": 1,
    "bing.com":1
};
 
var proxy = "SOCKS5 127.0.0.1:1080; SOCKS 127.0.0.1:1080; DIRECT;";
 
var direct = 'DIRECT;';
 
function FindProxyForURL(url, host) {
    var lastPos;
    do {
        if (domains.hasOwnProperty(host)) {
            return proxy;
        }
        lastPos = host.indexOf('.') + 1;
        host = host.slice(lastPos);
    } while (lastPos >= 1);
    return direct;
}
```

当然这么写，当链接相当多之后，效率可能会影响访问的延迟，造成不好的访问体验。

Windows SS 客户端文件目录直接生成了一个优化过的 PAC 文件，直接使用该文件来做小的修改。

## OPENVPN

### 安装

```sh
sudo apt install openvpn
```

### 启动

```sh
sudo openvpn --config client.conf
```

## 最后部署结果

* A 服务器
  * 部署 openvpn client
  * 部署 ss server
* B 服务器
  * 部署 ss-client 连接 openvpn 的 ss server 并共享 SOCK5 到 1080 端口
  * 部署 ss-client 连接到外网的 ss 服务器并共享 SOCK5 到 1070 端口
* 静态服务器
  * PAC 文件



#### PAC 文件规则

* 到公司域名的请求代理到服务器 B 的1080 端口。
* 到外国域名的请求代理到服务器 B 的1070 端口。
* 其他请求 `DIRECT`





