---
title: Nginx 降低 IO 负荷
date: 2020-03-10 13:19:25
tags: [ nginx ]
---

V 站上一个帖子提问：

> 硬盘 io 居高不下， nginx 能利用闲置的内存缓解一下 io 吗？
>
> https://www.v2ex.com/t/649310#reply8



有大佬回复:

> 当然可以，nginx 开启缓存功能非常强大。分为 4 级：
> 1，开启客户端 cache，设置 expires 头；
> 2，开启文件系统缓存，open_file_cache 相关；
> 3，开启代理缓存，设置 proxy_cache 相关；
> 4，开启脚本缓存，设置 fastcgi cache 相关；



按照他说的内容分析下这几个 Nginx 配置的功能。



# expires

> 开启客户端 cache ，设置 expires 头

```
location ~* \.(ico|js|css|png|gif|jpe?g)$ {
  expires 7d;
}
```



这个配置可以解决大部分的问题，尤其是反复请求同个文件导致的 IO 问题。他的效果就是在 Response 的 header 上加一个时间戳。在时间戳过期之前，客户端不会重新请求资源，而是利用本地的缓冲。



这个适用于浏览器，或者开启缓存功能的客户端。

优点：

1. 服务器不需要占用额外的内存或者存储。
2. 客户端重新加载不需要请求网络而是利用本地缓存，用户体验好。



缺点:

1. 如果请求的资源不重复则无效果
2. 如果请求该资源的客户端不是同一个也无效果
3. 缓存需要等待过期才会重新请求，这对迭代比较快的业务由影响。



## open_file_cache

> open_file_cache
>
> NGINX 缓存将最近使用的文件描述符和相关元数据（如修改时间，大小等）存储在缓存中。 
>
> **缓存不会存储所请求文件的内容。**

```
句法：	open_file_cache off;
        open_file_cache max=N [inactive=time];
默认：	open_file_cache off;
语境：	http，server，location
```



参数说明:

1. max

   设置缓存中的最大元素数; 在缓存溢出时，删除最近最少使用（LRU）的元素;

2. inactive

   定义一个时间，如果在此期间未访问该元素，则从该缓存中删除该元素; 默认情况下，它是60秒;

3. off

   禁用缓存。

> 范例 (引用原作者)
>
> open_file_cache max=102400 inactive=20s;
> max指定缓存数量 inactive是指经过多长时间文件没被请求后删除缓存。我指定了20s,所以等到至少20s不访问这个文件，相应缓存的这个文件的更改信息才会被删除。

---

### 其他指令

- open_file_cache_errors

  - 文件错误是否也同样缓存

  - > 句法：	open_file_cache_errors on | off;
    > 默认：	open_file_cache_errors off;
    > 语境：	http，server，location
    > 通过 open_file_cache 启用或禁用文件查找错误的缓存 。

- open_file_cache_min_uses

  - NGINX 将在非活动时间段之后从高速缓存中清除元素。 此指令可用于配置最小访问次数以将元素标记为活动使用。 默认情况下，最小访问次数设置为1次或更多次。

  - > 句法：	open_file_cache_min_uses number;
    > 默认：	open_file_cache_min_uses 1;
    > 语境：	http，server，location

- open_file_cache_valid

  - 这个是指多长时间检查一次缓存的有效信息。也就是说即使我一直访问这个文件，30s后会检查此文件的更改信息是否变化，发现变化就更新

  - > 句法：	open_file_cache_valid time;
    > 默认：	open_file_cache_valid 60s;
    > 语境：	http，server，location



## proxy_cache

> proxy_cache 用于代理服务器的文件请求，适合在使用了代理服务器的情况下，代理服务器的硬盘 IO 压力较小，而目标机 IO 压力较大的情况。
>
> 而且对动态生成的文件也起到了缓存的作用。
>
> 最核心的作用，是减少了目标服务器的 IO 压力，减少代理服务器到目标服务器请求的网络 IO 开销。



使用方法:

```
http {  
    ......  
    proxy_cache_path/data/nginx/tmp-test levels=1:2 keys_zone=tmp-test:100m inactive=7d max_size=1000g;  
}  
```

* proxy_cache_path 缓存文件路径

- levels 设置缓存文件目录层次；levels=1:2 表示两级目录

- keys_zone 设置缓存名字和共享内存大小

- inactive 在指定时间内没人访问则被删除

- max_size 最大缓存空间，如果缓存空间满，默认覆盖掉缓存时间最长的资源。

 

> proxy_cache  最主要一点是建立一层代理缓存。受到价格方面的影响，大部分服务器尤其是静态文件服务器会采用机械硬盘作为存储介质。而机械硬盘的 IO 性能较差，大批量请求会造成 IO 负责过高导致请求延迟，服务器爆炸。
>
> 而 proxy_cache 可以开辟一块高速缓存区，可以使用目前流行的 M.2 硬盘作为缓存区，甚至可以使用内存作为缓存区。在网站的场景下，大部分请求其实都是重复请求若干个相同的文件 (例如 css, js 文件)。 使用 proxy_cache 可以大大降低请求延迟，减少服务器负载。



> 在 V 站网友提出的这个案例中，如果负载高是因为请求 js，css 文件导致的，可以在 64 G 内存中分出 32G 作为高速缓存区。



## fastcgi_cache

和 proxy_cache 适用于静态文件不同，fastcgi_cache 适合作为动态文件或者 API 的缓存。与静态文件不同的是，动态文件往往是一个 URI 会根据不同的请求条件返回特定的数据。甚至哪怕请求一模一样，也可能返回不同的数据。因此，fastcgi_cache 相当于前面几种缓存，更需要复杂的配置和设置。



这里贴上网上关于 WordPress 的一份配置信息

```
http {
  ...
  fastcgi_connect_timeout 30s;
  # 全局设置， fastcgi_cache_path只能出现在http配置块中
  # fastcgi_cache_path设置全局的缓存路径、文件大小等，可多次出现设置不同的缓存池
  fastcgi_cache_path /var/cache/nginx levels=1:2 keys_zone=WORDPRESS:10m inactive=10m max_size=500m;
  fastcgi_cache_key $scheme$request_method$host$request_uri;
  # 设置使用过期缓存的情形：后端错误、超时等
  fastcgi_cache_use_stale error timeout invalid_header http_500 http_503;
  ...
  server {
    ...
    # 缓存策略指示变量
    set $skip_cache 0;
    # 缓存策略
    if ($query_string != "") {
      set $skip_cache 1;
    }
    if ($request_uri ~* "/wp-admin/|/xmlrpc.php|wp-.*.php|/feed/|sitemap(_index)?.xml") {
      set $skip_cache 1;
    }
    # 登录用户或发表评论者
    if ($http_cookie ~* "comment_author|wordpress_[a-f0-9]+|wp-postpass|wordpress_no_cache|wordpress_logged_in") {
      set $skip_cache 1;
    }
    location ~ \.php$ {
      ...
      # 使用的keyzone，这里使用http中定义的WORDPRESS
      fastcgi_cache WORDPRESS;
      # 是否跳过缓存
      fastcgi_cache_bypass $skip_cache;
      fastcgi_no_cache $skip_cache;
      # 后端返回成功的情况下缓存10分钟
      fastcgi_cache_valid 200 10m;
      # 添加header字段，指示缓存命中状态（调试使用）
      add_header X-Cache $upstream_cache_status;
    }
  }
}
```

1. 设置缓存池信息(fastcgi_cache_path)；

2. 设置缓存key(fastcgi_cache_key)；
3. 过期文件处理(fastcgi_cache_use_stale)
4. 缓存策略（fastcgi_cache_bypass/fastcgi_no_cache）
5. 后端请求成功时缓存时间（fastcgi_cache_valid）
6. 增加了调试信息头(add_header)



## 参考

1. https://juejin.im/post/5c6ac0a851882562ea7231b9
2. https://www.centos.bz/2016/10/using-nginx-caching-static-content/
3. https://www.cnblogs.com/Mwsoft/p/6272756.html
4. https://juejin.im/post/5b690c1e51882551374ae7c0