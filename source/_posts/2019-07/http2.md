---
title: 在树莓派上升级 Nginx 开启 HTTP/2
date: 2018-08-23 17:38:08
tags: [ http/2, raspberry, nginx ]
---
因为家里的路由器支持的 NAT 端口的数量只能有限的 20 个。随着自己乱七八糟的服务越来越多，不得不用家里的树莓派靠 Nginx 做了一层反向代理。支持根据域名访问不同的地址。

随着 Chrome 开始推荐 Https，用了自签证书搞了 Https。

然后看到了  HTTP/2。心想反正搞干脆搞好点。然后用我那个树莓派来折腾 HTTP/2。

> 不得不说树莓派真的适合学习 Linux。

## 关于 HTTP/2

HTTP/2 （原名HTTP/2.0）即超文本传输协议 2.0，是下一代HTTP协议。(From 百度百科)。

1. 支持异步连接多路复用
2. 头部压缩
3. 请求 / 响应管线化
4. 保持与 HTTP 1.1 语义的向后兼容
5. 支持服务器推送流(Service Push)
6. 对请求划分优先级



## OpenSSL

首先是要升级 OpenSSL。系统自带的 OpenSSL 版本不能满足 HTTP2 的需求。

在[官网](https://www.openssl.org/source)找到最近的[下载包](https://www.openssl.org/source/openssl-1.1.0i.tar.gz)。

```shell
$: wget https://www.openssl.org/source/openssl-1.1.0i.tar.gz
$: tar -xvf openssl-1.1.0i.tar.gz
$: cd openssl-1.1.0i
$: ./config
$: make -j4
```

## 编译 Nginx

首先获取原 Nginx 的编译参数。原来的 Nginx 是通过 apt 安装的。

```
/usr/local/nginx/sbin/nginx -V
```

在 [Nginx 官网](http://nginx.org/)下载最新的数据包[nginx-1.15.2.tar.gz](http://nginx.org/download/nginx-1.15.2.tar.gz)。

配置编译参数时，将原来的配置参数加上，并重新加入`--with-openssl=/home/maple/openssl-1.1.0i  --with-http_v2_module --with-http_ssl_module`

其中 ``--with-openssl=/home/maple/openssl-1.1.0i`是编译好的 openssl 的目录位置。

```
./configure --with-cc-opt='-g -O2 -fPIE -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2' --with-ld-opt='-Wl,-Bsymbolic-functions -fPIE -pie -Wl,-z,relro -Wl,-z,now' --prefix=/usr/share/nginx --conf-path=/etc/nginx/nginx.conf --http-log-path=/var/log/nginx/access.log --error-log-path=/var/log/nginx/error.log --lock-path=/var/lock/nginx.lock --pid-path=/run/nginx.pid --http-client-body-temp-path=/var/lib/nginx/body --http-fastcgi-temp-path=/var/lib/nginx/fastcgi --http-proxy-temp-path=/var/lib/nginx/proxy --http-scgi-temp-path=/var/lib/nginx/scgi --http-uwsgi-temp-path=/var/lib/nginx/uwsgi --with-debug --with-pcre-jit --with-ipv6 --with-http_ssl_module --with-http_stub_status_module --with-http_realip_module --with-http_auth_request_module --with-http_addition_module --with-http_dav_module --with-http_geoip_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_image_filter_module --with-http_v2_module --with-http_sub_module --with-http_xslt_module --with-stream --with-stream_ssl_module --with-mail --with-mail_ssl_module --with-openssl=/opt/openssl-1.1.0i  --with-http_v2_module --with-http_ssl_module
```

配置 ./configure 会提示缺失某些库。

```shell
# ./configure: error: the HTTP XSLT module requires the libxml2/libxslt
# libraries. You can either do not enable the module or install the libraries.

sudo apt-get install libxml2 -y
sudo apt-get install libxslt1-dev -y

# ./configure: error: the HTTP image filter module requires the GD library.
# You can either do not enable the module or install the libraries.

sudo apt-get install libgd2-xpm-dev -y
 
# ./configure: error: the GeoIP module requires the GeoIP library.
# You can either do not enable the module or install the library.
sudo apt-get install libgeoip-dev -y

# the HTTP rewrite module requires the PCRE library.
sudo apt install libpcre3 libpcre3-dev -y

# 完整版本
sudo apt install libpcre3 libpcre3-dev libgeoip-dev libgd2-xpm-dev libxslt1-dev libxml2 -y
```



> Ubuntu 18.04 libgd2-xpm-dev 会提示不存在，看了 v 站的评论，应该替换为 libgd-dev
>
> https://www.v2ex.com/t/542523



安装之后，如果还是提示库不存在。则可能需要刷新下 so 库。

```shell
ldconfig
```

编译还是一样:

```shell
make -j4
```

> 几个核心就 j 几。



编译完之后，不需要`make install`。

进入 `nginx-1.15.2/objs` 目录，将目录下 `nginx` 执行文件复制到 `/usr/sbin/`目录下覆盖原文件。(也可以备份原文件)。

然后重启 Nginx 服务:

```shell
systemctl restart nginx
```

Nginx 编译算是完成了。

## Nginx 配置

Nginx 配置 http/2 其实很简单。

在配置 conf 文件中。把 `listen 443 ssl `改成 `listen 443 ssl http2`。



## 检测 HTTP/2

最简单的办法是安装 Chrome 小插件`HTTP/2 and SPDY indicator`。

如果是 HTTP/2 的网页，小⚡️就会变成蓝色。


