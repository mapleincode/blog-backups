---
date: 2019-11-12 10:56:21
title: ELK 搭建
tags: ELK
---



## 起因

- 项目需求
- 公司某位大佬有记录文档可以参考





## ELK

ELK 平台是当前应用比较广泛的一套开源实时日志分析解决方案。ELK 由 ElasticSearch、Logstash 和 Kiabana 三个开源工具组成。

ELK = Elasticsearch + Logstash + Kibana

参考 & 下载地址: https://www.elastic.co/cn/products/



### Elasticsearch

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-elastic-search-color-64.svg)

Elasticsearch 是一个基于 JSON 的分布式搜索和分析引擎。



### Kibana

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-kibana-64-color.svg)

Kibana 可谓 Elastic Stack 的窗户。探索数据并管理堆栈。



### Beats

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-beats-64-color.svg)

Beats 是一个面向轻量型采集器的平台，这些采集器可从边缘机器发送数据。



### Logstash

![](/Users/maple/Desktop/SynologyDrive/code-try/blog-backups/source/_posts/2019-11/assets/logo-logstash-64-color.svg)

Logstash 是动态数据收集管道，拥有可扩展的插件生态系统。



## 搭建

### 1. 创建应用用户

```
useradd elk-test
```



### 2. 安装 Java 运行环境

略



### 3. 安装 ELK



#### 3.1 安装 ElasticSearch [ with x-pac ]

1. 切换 elk-test 用户
2. 





----

参考：公司某大佬遗留文档

参考：https://mp.weixin.qq.com/s/eVukCJI-U7uWsoO7uLacUA