---
title: frp 的使用
date: 2021-05-24 13:46:33
tags: [ frp ]
---

[TOC]



frp 堪称一神器。只要你拥有一台公网服务器，然后开启一个 tcp 端口，即可快乐的玩耍 frp。

> 个人方案原本是搭建在树莓派 4 上。无奈那玩意儿实在不稳定。
>
> 目前搭建在基于 esxi 的虚拟机上。
>
> 网络环境是普通的家用电信宽带，依赖 ddns 服务实现域名更新。



## 安装 & 启动

在 [Release](https://github.com/fatedier/frp/releases) 页面下载对应的文件包，解压。

主要包含以下文件

````sh
├── README.md
├── frpc # or frpc.exe
├── frpc.ini
├── frps # or frpc.exe
├── frps.ini
└── systemd
    ├── frpc.service
    ├── frpc@.service
    ├── frps.service
    └── frps@.service
````

> windows 下执行文件为 frpc.exe 和 frps.exe 。linux 和 unix 都是没有尾缀的。

frp 包含客户端 (frpc) 和服务端 （frps）。

```
frps -c frps.ini # 服务端，部署到有公网 IP 的服务器上
frpc -c frpc.ini # 客户端，部署到需要穿透的服务器或者 PC 上
```

#### 关于后端运行

1. nohup
2. tmux
3. pm2 (推荐)

> pm2 默认通过文件尾缀识别执行文件的内容。
>
> 除了写配置文件。最简单的办法是创建一个 `start.sh`，
>
> 然后 `pm2 start start.sh --name=frps`



## 基本知识

- http 和 tcp 的区别
- udp
- 反向代理



## frp 服务端配置范例

```bash
[common]                   # 相当于标记这段配置的名称，必须是 common
bind_port = 7000           # 绑定的默认端口，穿透服务访问的端口
token = tokenandpassword   # 访问服务的密码

# 日志我觉得没意义就没开启
# log_file = ./frps.log # 日志保存的时间
# log_max_days = 3 # 日志保存时间

# dashboard 控制台。可以查看连接、流量等信息
# 非必须开启，说实话一般自用的话也不会去看
dashboard_addr = 0.0.0.0   # 绑定地址，不懂直接 0.0.0.0
dashboard_port = 7500      # 控制台端口
dashboard_user = maple     # 控制台账号
dashboard_pwd = password   # 控制台密码

# 代理的相关设置，如果只是 tcp 转发可以不设置
# 这里要和 tcp 作为区别，因为 http 也是基于 tcp。所以省事的小伙伴直接 tcp 代理就好了。
# 就相当于是一个反向代理，因为所有 http 都是共用一个端口。所以必须需要一个域名绑定 ip 才能正常访问。
# 公网 dns, 本地 dns 甚至 /etc/hosts 都行。不熟悉的小伙伴还是放弃直接用 tcp 绑定吧。
vhost_http_port = 8080   # http 端口。注意如果用 80 端口的话，就必须有 root 权限。我这边是用 nginx 又做了一层 http 代理。 
subdomain_host = vvcat.cn # 默认域名的主域名，这个后面 http 转发会提到 
```



## frp 客户端配置范例

### 基础配置

```bash
[common]
server_addr = frp.com    # frp 服务端地址
server_port = 7000       # frp 服务端端口
token = tokenandpassword # frp 服务端 token 密码。必须一致。
```



### tcp

tcp 配置可以说最简单了

```bash
[client_code]          # code 全局唯一
type = tcp						 # type = tcp
local_ip = 127.0.0.1   # 被映射服务的 ip
local_port = 8888      # 被映射服务的端口
remote_port = 8888     # 映射出来的服务的端口
```

> 每一个 tcp 的配置都会占用一个服务端的端口，不同的 tcp 不能占用同一个端口



### http

http 的特点就是为了弥补 tcp 占用端口的问题。利用 http 服务的特性，使得不同 http 可以复用同一个端口。

http 端口就是服务端配置的 

```bash
[http_test]                  # code 全局唯一
type = http                  # type = http
local_ip = 127.0.0.1         # 被映射服务的 ip
local_port = 80              # 被映射的服务的端口
custom_domains = frp.xx.com  # http 访问的完整域名
# subdomain = frp            # http 访问的子域名
```



#### custom_domains & subdomain 的区别

服务端配置有个参数是 `subdomain_host`。我设置的 `vvcat.cn`。

两个参数有且只能填写一个。其中 `custom_domains` 支持多参数。两者区别主要是，`custom_domains` 填写的是完整的域名。而 `subdomain` 填写的是子域名部分。

例如 `subdomain` 参数填写的是 `frp`。则配合 `subdomain_host` 组合之后，完整的访问域名是 `frp.vvcat.cn`。

而 `custom_domains` 可以直接填写整个域名: `www.frptest.com`。



### stcp

stcp 是基于不暴露端口的要求实现的功能。

很多放在内网的服务存在安全性的问题。如果单单把内网穿透，并把服务暴露到外网，很明显存在很大的安全性问题。这时候 stcp 可以大展身手。

stcp 的原理就是，被访问的服务需要通过 frp 服务器注册到 frp 服务上。而访问者也需要运行一个 `vistor` 来访问 frp 服务。而 frp 服务把两者的 tcp 请求结合起来。这样就不需要把服务暴露到 frp 的端口上。达到安全隐蔽的目的。



穿透服务端的 client 配置 frpc-stcp-server.ini

```bash
[test_stcp]            # code
type = stcp            # type = stcp
role = server          # role = server 角色
local_ip = 127.0.0.1   # local_ip
local_port = 8888      # local_port
sk = test_stcp_sk      # sk 相当于凭证
```



访问者客户端的 client 配置 frpc-stcp-client.ini

```bash
[test_stcp_visitor]      # code
server_name = test_stcp  # server_name ，被穿透服务的 code。
type = stcp              # type = stcp
role = visitor           # role = visitor 访问者
bind_port = 80           # 绑定端口
sk = testzwynanhai       # sk 访问凭证，和上者配置一致
bind_addr = 127.0.0.1    # 绑定的 addr。这个绑定的概念取决于从哪里可以访问。例如如果绑定到 127.0.0.1。那就只有本地可以访问。如果绑定到你局域网的 ip。那就所有可以访问你设备 ip 的人都能访问。但是不能绑定到非本机所拥有的 ip。
```



被穿透的服务端（不是 frp 服务端） 执行

```
frpc -c frpc-stcp-server.ini
```

本地客户端执行

```
frpc -c frpc-stcp-client.ini
```

> 注意，两者都是用 frpc 启动的。



## frp 服务端详细配置

> 直接复制 https://gofrp.org/docs/reference/server-configures/

### 基础配置

| 参数                      | 类型   | 说明                                   | 默认值       | 可选值                          | 备注                                         |
| ------------------------- | ------ | -------------------------------------- | ------------ | ------------------------------- | -------------------------------------------- |
| bind_addr                 | string | 服务端监听地址                         | 0.0.0.0      |                                 |                                              |
| bind_port                 | int    | 服务端监听端口                         | 7000         |                                 | 接收 frpc 的连接                             |
| bind_udp_port             | int    | 服务端监听 UDP 端口                    | 0            |                                 | 用于辅助创建 P2P 连接                        |
| kcp_bind_port             | int    | 服务端监听 KCP 协议端口                | 0            |                                 | 用于接收采用 KCP 连接的 frpc                 |
| proxy_bind_addr           | string | 代理监听地址                           | 同 bind_addr |                                 | 可以使代理监听在不同的网卡地址               |
| log_file                  | string | 日志文件地址                           | ./frps.log   |                                 | 如果设置为 console，会将日志打印在标准输出中 |
| log_level                 | string | 日志等级                               | info         | trace, debug, info, warn, error |                                              |
| log_max_days              | int    | 日志文件保留天数                       | 3            |                                 |                                              |
| disable_log_color         | bool   | 禁用标准输出中的日志颜色               | false        |                                 |                                              |
| detailed_errors_to_client | bool   | 服务端返回详细错误信息给客户端         | true         |                                 |                                              |
| heart_beat_timeout        | int    | 服务端和客户端心跳连接的超时时间       | 90           |                                 | 单位：秒                                     |
| user_conn_timeout         | int    | 用户建立连接后等待客户端响应的超时时间 | 10           |                                 | 单位：秒                                     |
| udp_packet_size           | int    | 代理 UDP 服务时支持的最大包长度        | 1500         |                                 | 服务端和客户端的值需要一致                   |
| tls_cert_file             | string | TLS 服务端证书文件路径                 |              |                                 |                                              |
| tls_key_file              | string | TLS 服务端密钥文件路径                 |              |                                 |                                              |
| tls_trusted_ca_file       | string | TLS CA 证书路径                        |              |                                 |                                              |

### 权限验证

| 参数                        | 类型   | 说明                   | 默认值 | 可选值      | 备注                               |
| --------------------------- | ------ | ---------------------- | ------ | ----------- | ---------------------------------- |
| authentication_method       | string | 鉴权方式               | token  | token, oidc |                                    |
| authenticate_heartbeats     | bool   | 开启心跳消息鉴权       | false  |             |                                    |
| authenticate_new_work_conns | bool   | 开启建立工作连接的鉴权 | false  |             |                                    |
| token                       | string | 鉴权使用的 token 值    |        |             | 客户端需要设置一样的值才能鉴权通过 |
| oidc_issuer                 | string | oidc_issuer            |        |             |                                    |
| oidc_audience               | string | oidc_audience          |        |             |                                    |
| oidc_skip_expiry_check      | bool   | oidc_skip_expiry_check |        |             |                                    |
| oidc_skip_issuer_check      | bool   | oidc_skip_issuer_check |        |             |                                    |

### 管理配置

| 参数                 | 类型   | 说明                               | 默认值 | 可选值 | 备注                            |
| -------------------- | ------ | ---------------------------------- | ------ | ------ | ------------------------------- |
| allow_ports          | string | 允许代理绑定的服务端端口           |        |        | 格式为 1000-2000,2001,3000-4000 |
| max_pool_count       | int    | 最大连接池大小                     | 5      |        |                                 |
| max_ports_per_client | int    | 限制单个客户端最大同时存在的代理数 | 0      |        | 0 表示没有限制                  |
| tls_only             | bool   | 只接受启用了 TLS 的客户端连接      | false  |        |                                 |

### Dashboard, 监控

| 参数              | 类型   | 说明                          | 默认值  | 可选值 | 备注                                                         |
| ----------------- | ------ | ----------------------------- | ------- | ------ | ------------------------------------------------------------ |
| dashboard_addr    | string | 启用 Dashboard 监听的本地地址 | 0.0.0.0 |        |                                                              |
| dashboard_port    | int    | 启用 Dashboard 监听的本地端口 | 0       |        |                                                              |
| dashboard_user    | string | HTTP BasicAuth 用户名         |         |        |                                                              |
| dashboard_pwd     | string | HTTP BasicAuth 密码           |         |        |                                                              |
| enable_prometheus | bool   | 是否提供 Prometheus 监控接口  | false   |        | 需要同时启用了 Dashboard 才会生效                            |
| asserts_dir       | string | 静态资源目录                  |         |        | Dashboard 使用的资源默认打包在二进制文件中，通过指定此参数使用自定义的静态资源 |

### HTTP & HTTPS

| 参数               | 类型   | 说明                                            | 默认值 | 可选值 | 备注                                      |
| ------------------ | ------ | ----------------------------------------------- | ------ | ------ | ----------------------------------------- |
| vhost_http_port    | int    | 为 HTTP 类型代理监听的端口                      | 0      |        | 启用后才支持 HTTP 类型的代理，默认不启用  |
| vhost_https_port   | int    | 为 HTTPS 类型代理监听的端口                     | 0      |        | 启用后才支持 HTTPS 类型的代理，默认不启用 |
| vhost_http_timeout | int    | HTTP 类型代理在服务端的 ResponseHeader 超时时间 | 60     |        |                                           |
| subdomain_host     | string | 二级域名后缀                                    |        |        |                                           |
| custom_404_page    | string | 自定义 404 错误页面地址                         |        |        |                                           |

### TCPMUX

| 参数                    | 类型 | 说明                         | 默认值 | 可选值 | 备注                                     |
| ----------------------- | ---- | ---------------------------- | ------ | ------ | ---------------------------------------- |
| tcpmux_httpconnect_port | int  | 为 TCPMUX 类型代理监听的端口 | 0      |        | 启用后才支持 TCPMUX 类型的代理，默认不启 |

