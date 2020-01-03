---
title: 使用 docker-compose 部署 postgres
date: 2019-01-04 20:37

---

# 部署 postgres

## 部署

`docker-compose.yaml` 配置文件如下，可以通过 [compose/postgres](https://github.com/shfshanyue/op-note/blob/master/compose/postgres/docker-compose.yaml) 查看我的配置

``` yaml
version: '3'

services:
  db:
    image: postgres:12-alpine
    restart: always
    ports:
      - 5432:5432
    volumes:
      - ./pg-data:/var/lib/postgsesql/data
    labels:
      - "traefik.http.routers.db.rule=Host(`db.shanyue.local`)"

# 使用已存在的 traefik 的 network
networks:
  default:
    external:
      name: traefik_default
```

启动服务

``` bash
$ docker-compose up -d
```

## 连接数据库

使用 `docker-compose exec` 测试是否能够正常连接数据库

``` bash
$ docker-compose exec db psql -U postgres
```

连接数据库正常

## 使用 pgcli 连接数据库

如果 `psql` 是记事本，而 `pgcli` 则是带有代码高亮功能的专用编辑器。在日常开发中，使用 `pgcli` 足以应付生产环境多个数据库的配置管理

在 `MAC` 上使用 `brew` 安装 `pgcli`

``` bash
$ brew install pgcli
```

连接数据库，连接成功

``` bash
$ pgcli -h db.shanyue.local -U postgres
```