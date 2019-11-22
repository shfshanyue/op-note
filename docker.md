---
title: docker 简易入门
keywords: docker
date: 2019-11-19T20:02:20+08:00
categories:
  - 运维
  - 后端
thumbnail: https://docs.docker.com/engine/images/architecture.svg
tags:
  - devops
---

![docker architecture](https://docs.docker.com/engine/images/architecture.svg)

`docker` 使应用部署更加轻量，可移植，可扩展。更好地环境隔离也更大程度地避免了生产环境与测试环境不一致的巨大尴尬。由于 `docker` 轻便可移植的特点也极大促进了 `CI/CD` 的发展。

<!--more-->

## 安装 docker

> 参考在 centos 上安装 docker 的官方文档: <https://docs.docker.com/install/linux/docker-ce/centos/>

安装依赖

``` bash
$ yum install -y yum-utils device-mapper-persistent-data lvm2
```

添加 `docker` 的镜像源，如果在国内，添加阿里云的镜像源

``` bash
# 安装 docker 官方的镜像源
$ yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 如果在国内，安装阿里云的镜像
$ yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

安装指定版本的 `docker` 并且启动服务

``` bash
# 安装 docker
$ yum install -y docker-ce

# 安装指定版本号的 docker，以下是 k8s 官方推荐的 docker 版本号 (此时，k8s 的版本号在 v1.16)
$ yum install -y docker-ce-18.06.2.ce

$ systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.

$ systemctl start docker
```

当 `docker` 安装成功后，可以使用以下命令查看版本号

``` shell
$ docker --version
Docker version 18.06.2-ce, build 6d37f41

# 查看更详细的版本号信息
$ docker version

# 查看docker的详细配置信息
$ docker info
```

## docker daemon

`dockerd` 是 `docker` 的后台进程，而 `dockerd` 可以通过配置文件进行配置，在 linux 下在 `/etc/docker/daemon.json`，详细可以参考 [官方文档](https://docs.docker.com/engine/reference/commandline/dockerd/)。

``` shell
$ mkdir /etc/docker

# 设置 docker daemon
$ cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

# 重启 docker
$ systemctl daemon-reload
$ systemctl restart docker
```

## 底层原理

`docker` 底层使用了一些 `linux` 内核的特性

### namespace

`docker` 使用 `linux namespace` 构建隔离的环境，它由以下 `namespace` 组成

+ `pid`: 隔离进程
+ `net`: 隔离网络
+ `ipc`: 隔离 IPC
+ `mnt`: 隔离文件系统挂载
+ `uts`: 隔离hostname
+ `user`: 隔离uid/gid

### control groups

也叫 `cgroups`，限制资源配额，比如某个容器只能使用 `100M` 内存

### Union file systems

`UnionFS` 是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加。`docker` 的镜像与容器就是分层存储，可用的存储引擎有 `aufs`，`overlay` 等。

关于分层存储的详细内容可以查看官方文档 [docker: About storage drivers](https://docs.docker.com/storage/storagedriver/)

## 镜像

镜像是一份用来创造容器的配置文件，而容器可以视作最小型的一个操作系统。

**`docker` 的镜像和容器都使用了 `unionFS` 做分层存储，镜像作为只读层是共享的，而容器在镜像之上附加了一层可写层，最大程度地减少了空间的浪费，详见下图**

![分层存储](https://docs.docker.com/storage/storagedriver/images/sharing-layers.jpg)

### 镜像仓库与拉取

大部分时候，我们不需要自己构建镜像，我们可以在[官方镜像仓库](https://hub.docker.com/explore/)拉取镜像

可以简单使用命令 `docker pull` 拉取镜像。拉取镜像后可以使用 `docker inspect` 查看镜像信息

``` bash
# 加入拉取一个 node:alpine 的镜像
$ docker pull node:alpine

# 查看镜像信息
$ docker inspect node:alpine

# 列出所有镜像
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
node                alpine              f20a6d8b6721        13 days ago         105MB
mongo               latest              965553e202a4        2 weeks ago         363MB
centos              latest              9f38484d220f        8 months ago        202MB
```

### 构建镜像与发布

但并不是所有的镜像都可以在镜像仓库中找到，另外我们也需要为我们自己的业务应用去构建镜像。

使用 `docker build` 构建镜像，`docker build` 会使用当前目录的 `dockerfile` 构建镜像，至于 `dockerfile` 的配置，参考下节。

`-t` 指定标签

``` bash
# -t node-base:10: 镜像以及版本号
# .: 指当前路径
$ docker build -t node-base:10 .
```

当构建镜像成功后可以使用 `docker push` 推送到镜像仓库

## Dockerfile

在使用 `docker` 部署自己应用时，往往需要自己构建镜像。`docker` 使用 `Dockerfile` 作为配置文件构建镜像，简单看一个 `node` 应用构建的 `dockerfile`

``` dockerfile
FROM node:alpine

ADD package.json package-lock.json /code/
WORKDIR /code

RUN npm install --production

ADD . /code

CMD npm start
```

### FROM

### ADD

## 容器

镜像与容器的关系，类似于代码与进程的关系。

+ `docker run` 创建容器
+ `docker stop` 停止容器
+ `docker rm` 删除容器

### 创建容器

基于 `nginx` 镜像创建一个最简单的容器：启动一个最简单的 http 服务

``` bash
$ docker run -d --name nginx -p 8888:80 nginx:alpine
```

其中:

+ `-d`: 启动一个 `daemon` 进程
+ `--name`: 为容器指定名称
+ `-p host-port:container-port`: 宿主机与容器端口映射，方便容器对外提供服务
+ `nginx:alpine`: 基于该镜像创建容器

### 容器管理

`docker ps` 列出所有容器

``` bash
$ docker ps
CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS              PORTS                    NAMES
404e88f0d90c        nginx:alpine         "nginx -g 'daemon of…"   4 minutes ago       Up 4 minutes        0.0.0.0:8888->80/tcp     nginx
498e7d74fb4f        nginx:alpine         "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes        80/tcp                   lucid_mirzakhani
2ce10556dc8f        redis:4.0.6-alpine   "docker-entrypoint.s…"   2 months ago        Up 2 months         0.0.0.0:6379->6379/tcp   apolloserverstarter_redis_1
```

`docker port` 查看容器端口映射

``` bash
$ docker port nginx
80/tcp -> 0.0.0.0:8888
```

`docker stats` 查看容器资源占用 

``` bash
$ docker stats nginx
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
404e88f0d90c        nginx               0.00%               1.395MiB / 1.796GiB   0.08%               632B / 1.27kB       0B / 0B             2
```

## 容器资源配额限制
