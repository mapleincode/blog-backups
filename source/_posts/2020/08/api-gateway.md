---
title: API 网关功能
date: 2020-09-05 21:15:01
tags: [ other ]
---

网关封装了底层系统并与客户端分离，为客户端提供了与微服务系统进行通信的单个入口点。

整合 API 网关整合了一些边缘的重复功能，无需让每个微服务都实现它们。它包括如下功能：

- 认证和授权
- 服务发现集成
- 缓存响应结果
- 重试策略、熔断器、QoS
- 限速和节流
- 负载均衡
- log 日志、链路追踪、关联
- Header、query 字符串 以及 claims 转义
- IP 白名单
- IAM
- 集中式日志管理（服务之间的 transaction ID、错误日志等）
- 身份的提供方，验证与授权

from https://www.infoq.cn/article/qXCl87G3fsIUbNVuLKOI

