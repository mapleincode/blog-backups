---
date: 2021-04-08 18:38:24
tags: nginx
title: Nginx 支持 WebSocket
---



```nginx
# 编辑nginx.conf，在http区域内一定要添加下面配置：
map $http_upgrade $connection_upgrade {
    default upgrade;
    '' close;
}
 
# map指令的作用：
# 该作用主要是根据客户端请求中$http_upgrade 的值，来构造改变$connection_upgrade的值，即根据变量$http_upgrade的值创建新的变量$connection_upgrade，
# 创建的规则就是{}里面的东西。其中的规则没有做匹配，因此使用默认的，即 $connection_upgrade 的值会一直是 upgrade。然后如果 $http_upgrade 为空字符串的话，
# 那值会是 close。
 
 
# 编辑vhosts下虚拟主机的配置文件，在location匹配配置中添加如下内容：
proxy_http_version 1.1;
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection "Upgrade";
 
示例如下：
upstream socket.kevin.com {
  hash $remote_addr consistent;
  server 10.0.12.108:9000;
	server 10.0.12.109:9000;
}
 
location / {
	proxy_pass http://socket.kevin.com/;
	proxy_set_header Host $host:$server_port;
  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection "upgrade";
}
```

