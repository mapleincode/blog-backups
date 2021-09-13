---
title: WordPress 绑定多域名
date: 2020-04-01 00:37:26
tags: [ php ]
---

WordPress 是基于 PHP 的博客服务。大量配置都是基于 MySQL 进行配置。

而域名绑定也被写入 SQL，包括 `WP_SITEURL` 和 `WP_HOME` 两个参数。



但是有些时候，WP 可能会拥有多个域名。所以需要对 `wp-config.php` 进行改动来实现。



首先找到 `wp-config.php`配置文件，在**define('WP_DEBUG', false);**  加入

```php
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
```



如果需要从特定的域名进行访问，可以这么修改:

```php
$domain = array("www.a.com", "www.b.com", "www.c.com"); 
if(in_array($_SERVER['HTTP_HOST'], $domain)){
    define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
    define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
}
```



除了配置之外，还有静态资源的地址，可以修改成根目录的方式

```php
define( 'WP_CONTENT_URL', '/wp-content');
```



参考:

1. https://www.wpcom.cn/tutorial/7.html