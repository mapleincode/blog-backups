---
title: Nginx 部署 react 项目返回 404
date: 2018-05-16 00:36:30
tags: [ nginx, reactjs ]
---

前端时间苦学 React.js，作为愚蠢的后端开发，在面对如此复杂的前端框架，真的是...



用 dva.js 算是完成了第一个可以用的小工具，在树莓派上编译成 html ，然后新建一个 nginx server，部署。



直接进根目录是没有问题的，但是如果我直接选择子目录类似: `/utils`。却返回了 404。



查了下百度，说法是因为 Nginx 是根据路径去查找文件，但是 react-router 默认编译后只有一个 `index.html`。



所以办法是修改 nginx.conf

```nginx
server {  
  ...
  location / {
    try_files $uri /index.html;
  }
}
```

无论访问什么连接，一律返回 `/index.html`。

问题解决。
