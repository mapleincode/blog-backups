title: GitLab 推送了更新(三) - Nginx 反向代理
date: 2017-11-03 19:18:41
tags: [ Nginx ]
---

>GitLab 搭建在家里的一台服务器上，并没有直接暴露到外网。为了可以外网访问，所以需要把端口转发到内网(NAT)。因为用了渣 TP 路由器，只支持转发 20 个端口到外网。为了节省端口资源，所以打算使用树莓派上的 Nginx 作为反向代理，使多个 Domain 同时使用一个端口。

## Nginx 安装

无脑安装:

```bash
sudo apt update;
sudo apt install nginx;
```

查看 nginx 服务运行状态:

```bash
maple@localhost:~/$ sudo service nginx status

● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
   Active: active (running) since 二 2017-10-24 20:56:03 CST; 1 weeks 2 days ago
 Main PID: 803 (nginx)
   CGroup: /system.slice/nginx.service
           ├─  803 nginx: master process /usr/sbin/nginx -g daemon on; master_process on
           └─11921 nginx: worker process
```

nginx 已经运行了。

## Server 配置

默认 server

```nginx
server {
	listen 80 default_server;
	listen [::]:80 ipv6only=on default_server;

	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.php index.html index.htm index.nginx-debian.html;
	error_page 404 /404.html;

	server_name _;

	location / {
		try_files $uri $uri/ =404;
	}
}

```

GitLab server

```nginx
server {
    listen 80;
    server_name git.mapleincode.com;
    location / {
        proxy_redirect     off;
        proxy_set_header   Host             $host:$server_port;
        proxy_set_header   X-Real-IP        $remote_addr;
        proxy_set_header   X-Forwarded-For 	$proxy_add_x_forwarded_for;
		proxy_pass http://192.168.2.110:5111;
    }
}
```

配置完成。

## 要注意的坑

其实这不是第一次尝试迁移 GitLab 。之前迁移过，但是不明白为什么当用户登录的时候会跳转到 80 端口(因为电信把 80 封了，所以只能使用 80 意外的端口。本来可以用 443 端口现在也没了 QAQ。)。

因为别的系统服务转发都没有问题，于是我一直以为是因为我 GitLab 没有配置好。后来发现好像自己的 Nginx 配置有点问题。
当时是这么配置的:

```bash
...
proxy_set_header   Host             $host;
...
```

在百度上翻了好几个教程，都是这么配置的。
后来忽然想到，转发的时候除了要让被代理的服务器知道原来的域名(`$host`)，也是否需要将原来请求访问的端口也并写入`header`。于是我去查询了`$host`相关的关键字，终于找到了另外一个关键字`$server_port`。

吐血...

后面想想他们之所以不需要配置`$server_port`，多半是因为他们反向代理的机子本来就是 80 端口，而我的不是......

配置完成之后，GitLab 跳转正常。

只能说明两个点:

1. 百度不靠谱。
2. 博客教程不靠谱。

还是需要更深入的去了解和思考一些细节。
