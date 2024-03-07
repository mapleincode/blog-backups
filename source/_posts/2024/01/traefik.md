---
title: "Traefik 使用和配置"
date: "2024-01-31 17:10:09"
tags: [docker, traefik]
---



Traefik  的作用是基于 Docker 网络来暴露服务，通过域名的方式来分发对容器的请求。

在网上找到一老哥的介绍，基本已经很完善了。

[Traefik v3.0 Docker 全面使用指南：基础篇](https://soulteary.com/2023/07/18/traefik-v3-docker-comprehensive-user-guide-basics.html)



但是就最后的结果，因为我不想再部署一套 https 证书的申请获取，于是我简化成了纯 http 版本：



```yaml
version: "3"

services:
  traefik:
    image: traefik:v3.0.0-beta3
    restart: always
    ports:
      - target: 80
        published: 80
        protocol: tcp
        mode: host
      # - target: 443
      #   published: 443
      #   protocol: tcp
      #   mode: host
    command:
      - "--global.sendanonymoususage=false"
      - "--global.checknewversion=false"
      - "--api=true"
      - "--api.dashboard=true"
      - "--api.insecure=true"
      - "--api.debug=false"
      - "--ping=true"
      - "--log.level=INFO"
      - "--log.format=common"
      - "--accesslog=false"
      - "--entrypoints.http.address=:80"
      # - "--entrypoints.https.address=:443"
      - "--providers.docker=true"
      - "--providers.docker.watch=true"
      - "--providers.docker.exposedbydefault=false"
      - "--providers.docker.endpoint=unix:///var/run/docker.sock"
      - "--providers.docker.useBindPortIP=false"
      - "--providers.docker.network=traefik"
      - "--providers.file=true"
      - "--providers.file.watch=true"
      - "--providers.file.directory=/etc/traefik/config"
      - "--providers.file.debugloggeneratedtemplate=true"
    networks:
      - traefik
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=traefik"

      # 定义插件
      # - "traefik.http.middlewares.gzip.compress=true"
      # - "traefik.http.middlewares.redir-https.redirectscheme.scheme=https"
      # - "traefik.http.middlewares.redir-https.redirectscheme.permanent=false"

      # 使用插件
      # - "traefik.http.routers.traefik-dashboard.middlewares=redir-https@docker"
      # - "traefik.http.routers.traefik-dashboard-secure.middlewares=gzip@docker"
      # - "traefik.http.routers.traefik-dashboard-api-secure.middlewares=gzip@docker"

      - "traefik.http.routers.traefik-dashboard.entrypoints=http"
      - "traefik.http.routers.traefik-dashboard.rule=Host(`traefik-console.vvcat.cn`)"
      - "traefik.http.routers.traefik-dashboard.service=dashboard@internal"

      - "traefik.http.routers.traefik-dashboard-api.entrypoints=http"
      - "traefik.http.routers.traefik-dashboard-api.rule=Host(`traefik-console.vvcat.cn`) && PathPrefix(`/api`)"
      - "traefik.http.routers.traefik-dashboard-api.service=api@internal"

      # - "traefik.http.routers.traefik-dashboard-secure.entrypoints=https"
      # - "traefik.http.routers.traefik-dashboard-secure.tls=true"
      # - "traefik.http.routers.traefik-dashboard-secure.rule=Host(`traefik-console.vvcat.cn`)"
      # - "traefik.http.routers.traefik-dashboard-secure.service=dashboard@internal"

      # - "traefik.http.routers.traefik-dashboard-api-secure.entrypoints=https"
      # - "traefik.http.routers.traefik-dashboard-api-secure.tls=true"
      # - "traefik.http.routers.traefik-dashboard-api-secure.rule=Host(`traefik-console.vvcat.cn`) && PathPrefix(`/api`)"
      # - "traefik.http.routers.traefik-dashboard-api-secure.service=api@internal"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./config/:/etc/traefik/config/:ro
    healthcheck:
      test:
        [
          "CMD-SHELL",
          "wget -q --spider --proxy off localhost:8080/ping || exit 1",
        ]
      interval: 3s
      retries: 10
    logging:
      driver: "json-file"
      options:
        max-size: "1m"

networks:
  traefik:
    external: true

```

其中，有几个注意的点，



容器的 traefik 配置基本是基于 labels ，标签。



## 配置启用 traefik

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.docker.network=traefik"
```

这两个定义的是容易是否启用 traefik 作为网络网关和选择使用的网络。



## 配置 Router

```yaml
- "traefik.http.routers.sso-http.entrypoints=http"
- "traefik.http.routers.sso-http.rule=Host(`$SSO_SERVER_DOMAIN`)"
```

在这里，定义了一个 router `sso-http`，并且在他的路由规则 rule 绑定了一个域名。而 entrypoints 则定义了路由后的地址是 http，也就是 traefik 定义的 80 端口。



如果定义的 Router 是 https，则需要将 entrypoints 定义为 https ，并启用 tls

```yaml
- "traefik.http.routers.sso-https.entrypoints=https"
- "traefik.http.routers.sso-https.tls=true"
- "traefik.http.routers.sso-https.rule=Host(`$SSO_SERVER_DOMAIN`)"
```



rule 还支持路径前缀匹配

```yaml
- "traefik.http.routers.outline-http-assets.rule=Host(`${DOCKER_OUTLINE_HOSTNAME}`) && (PathPrefix(`/static`) || PathPrefix(`/api`))"
```



## 配置 Service

```yaml
- "traefik.http.services.sso-backend.loadbalancer.server.scheme=http"
- "traefik.http.services.sso-backend.loadbalancer.server.port=80"
```

这里定义了一个 service 服务，并且配置了其负载均衡的后端的配置，比如服务是 http，并且端口是 80；

> 默认只有一个 service 就不需要额外定义，否则需要在 router 上指定对应的服务
>
> 例如：
>
> ```yaml
> - "traefik.http.routers.minio-http.service=minio-backend"
> ```



> traefik 的配置中，出现了 api@internal 和 dashboard@internal 两个service。推测应该是 traefik 的内部服务。
>
> 推测和启动命令中的`--api.dashboard=true` 和 `--api.insecure=true` 有关



## 配置中间件

作者给的 Demo 里定义了两个中间件

定义中间件：

```yaml
# 开启 GZip 压缩
- "traefik.http.middlewares.minio-gzip.compress=true"

# 开启 http 重定向
- "traefik.http.middlewares.minio-redir-https.redirectScheme.scheme=https"
```



配置中间件：

```yaml
- "traefik.http.routers.minio-http.middlewares=minio-redir-https"
- "traefik.http.routers.minio-https.middlewares=minio-gzip"
```

