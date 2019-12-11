---
title: traefik 简易入门
date: 2019-12-07T16:31:01+08:00
thumbnail: https://docs.traefik.io/assets/img/traefik-architecture.png
categories:
  - 后端
  - 运维
tags:
  - devops
---

`traefik` 与 `nginx` 一样，是一款优秀的反向代理工具，或者叫 `edge router`。至于使用它的原因则基于以下几点

+ 无须重启即可更新配置！
+ 自动的服务发现与负载均衡
+ 与 `docker` 的完美集成，基于 `container label` 的配置
+ `metrics` 的支持，对 `prometheus` 和 `k8s` 的集成
+ 漂亮的 `dashboard` 界面
+ 尝试一下...

接下来讲一下它的安装，基本功能以及配置。本篇文章的 `traefik` 的版本号是 `v2.0.6`

<!--more-->

![traefik quickstart](https://docs.traefik.io/assets/img/quickstart-diagram.png)

## 快速开始

我们使用 `traefik:v2.0` 作为镜像启动 `traefik` 服务。`docker-compose.yaml` 配置文件如下

``` yaml
version: '3'

services:
  traefik:
    image: traefik:v2.0
    command: --api.insecure=true --providers.docker
    ports:
      - "80:80"
      - "8080:8080"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

此时我们使用命令 `docker-compose up -d` 开启 `traefik` 服务

接下来我们使用 `docker-compose` 启动一个简单的 `http` 服务，`docker-compose.yaml` 配置文件如下

``` yaml
version: '3'

services:
  # 改镜像会暴露出自身的 `header` 信息
  whoami:
    image: containous/whoami
    labels:
      # 设置Host 为 whoami.docker.localhost 进行域名访问
      - "traefik.http.routers.whoami.rule=Host(`whoami.docker.localhost`)"

# 使用已存在的 traefik 的 network
networks:
  default:
    external:
      name: traefik_default
```

那 `whoami` 这个 `http` 服务做了什么事情呢

1. 暴露了一个 `http` 服务，主要提供一些 `header` 以及 `ip` 信息
1. 设置了 `labels`，设置该服务的 `Host` 为 `whoami.docker.localhost`，给 `traefik` 提供标记

此时我们可以通过主机名 `whoami.docker.localhost` 来访问 `whoami` 服务，我们使用 `curl` 做测试

``` bash
$ curl -H Host:whoami.docker.localhost http://127.0.0.1
Hostname: bc3e8f1a5066
IP: 127.0.0.1
IP: 172.21.0.2
RemoteAddr: 172.21.0.1:37852
GET / HTTP/1.1
Host: whoami.docker.localhost
User-Agent: curl/7.29.0
Accept: */*
Accept-Encoding: gzip
X-Forwarded-For: 127.0.0.1
X-Forwarded-Host: whoami.docker.localhost
X-Forwarded-Port: 80
X-Forwarded-Proto: http
X-Forwarded-Server: 8.8.8.8
X-Real-Ip: 127.0.0.1
```

服务正常访问。此时如果把 `Host` 配置为自己的域名，则已经可以使用自己的域名来提供服务

## 配置文件

使用 `./traefik --configFile=traefik.toml` 可以读取配置文件 `traefik.toml`。

基本配置文件可以通过 [traefik.sample.toml](https://raw.githubusercontent.com/containous/traefik/master/traefik.sample.toml) 获取

一个简单的配置文件及释义如下

### 日志

``` toml
[accessLog]

filePath = "./traefik-access.json"

format = "json"
```

日志文件配置为 `json` 格式，结构化数据方便调试

### entryPoint

``` toml
[entryPoints]
  [entryPoints.web]
    address = ":80"

  [entryPoints.web-secure]
    address = ":443"
```

考虑到隐私以及安全，不对外公开的服务可以配置 `Basic Auth`，`Digest Auth` 或者 `WhiteList`，或者直接搭建 VPN，在内网内进行访问。至于 `Basic Auth` 等，可以参考 [traefik middlewares](https://docs.traefik.io/middlewares/overview/)

### prometheus metrics

``` toml
[metrics.prometheus]
  buckets = [0.1,0.3,1.2,5.0]
  entryPoint = "metrics"
```

[Prometheus](https://prometheus.io/) 作为时序数据库，可以用来监控 traefik 的日志，支持更加灵活的查询，报警以及可视化。traefik 默认设置 prometheus 作为日志收集工具。另外可以使用 `grafana` 做为 `prometheus` 的可视化工具。

## Docker provider

traefik 会监听 `docker.sock`，根据容器的 `label` 进行配置。

## File provider
