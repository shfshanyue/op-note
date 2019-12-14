# 使用 openvpn 与集群内部服务通信

当我们访问集群内部服务，如数据库，缓存，`traefik Dashboard`，`gitlab` 时，如果直接暴露在公网中，则会造成很大安全隐患，而使用 `Basic Auth` 等也不能完全避开问题。此时在开发环境使用 `vpn` 连接集群是一个不错的选择

> Q: [在集群中如何保证私有服务的安全性](https://github.com/shfshanyue/Daily-Question/issues/125)

<!--more-->

+ 原文链接: [使用 openvpn 与集群内部服务通信](https://github.com/shfshanyue/op-note/blob/master/openvpn.md)
+ 系列文章: [当我有台服务器时我做了什么](https://github.com/shfshanyue/op-note)

## 部署 openvpn

准备 `docker-compose.yaml` 如下，我们同样把它置于网络 `traefik-default` 下。`traefik-default` 是 `traefik` 用以服务发现的所有应用的入口网关，详情可参考上一节 [traefik 简易入门](https://github.com/shfshanyue/op-note/blob/master/traefik.md)

``` yaml
version: '3'
services:
  openvpn:
    cap_add:
      - NET_ADMIN
    image: kylemanna/openvpn
    container_name: openvpn
    ports:
      - "1194:1194/udp"
    restart: always
    volumes:
      - ./openvpn-data/conf:/etc/openvpn

networks:
  default:
    external:
      name: traefik_default
```

初始化配置文件，注意替换掉你真实的公网IP，并且需要在 `iptables` 或者阿里云安全组中把 1194 端口给暴露出来

``` bash
$ docker run -v $(pwd)/openvpn-data/conf:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_genconfig -u udp://<IP>
```

初始化证书，此时需要交互式确认密码

``` bash
$ docker run -v $(pwd)/openvpn-data/conf:/etc/openvpn --log-driver=none --rm -it kylemanna/openvpn ovpn_initpki
```

生成客户端证书

``` bash
$ docker run -v $(pwd)/openvpn-data/conf:/etc/openvpn --log-driver=none --rm -it kylemanna/openvpn easyrsa build-client-full shanyue nopass
```

生成客户端配置文件 `shanyue.ovpn`，成功生成后需要把该文件传到本地。

``` bash
$ docker run -v $(pwd)/openvpn-data/conf:/etc/openvpn --log-driver=none --rm kylemanna/openvpn ovpn_getclient shanyue > shanyue.ovpn
```

## 客户端连接

使用 `rsync` 把生成的配置文件传到本地环境

``` bash
rsync -avhzP dev:/path/openvpn/shanyue.ovpn .
```

使用 [tunnelblick](https://tunnelblick.net/) 打开配置文件，点击连接 vpn，连接成功

## 访问内部集群

在 [traefik 简易入门](https://github.com/shfshanyue/op-note/blob/master/traefik.md) 中，我们在集群中配置了 `whoami.docker.localhost` 用以访问 `whoami` 服务。

**此时我们通过在本地环境连接 VPN，查看在本地是否可以访问集群服务**

查看 `traefik` 的网络 IP

``` bash
$ docker network inspect traefik-default

        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
```

找到 IP 后，我们通过 `curl` 查看服务是否可以正常访问

``` bash
$ curl -H Host:whoami.docker.localhost http://172.18.0.1
Hostname: eb290c85a325
IP: 127.0.0.1
IP: 172.18.0.2
RemoteAddr: 172.18.0.3:34012
GET / HTTP/1.1
Host: whoami.docker.localhost
User-Agent: curl/7.54.0
Accept: */*
Accept-Encoding: gzip
X-Forwarded-For: 172.18.0.1
X-Forwarded-Host: whoami.docker.localhost
X-Forwarded-Port: 80
X-Forwarded-Proto: http
X-Forwarded-Server: 11e1f03c87ef
X-Real-Ip: 172.18.0.1
```

此时在本地环境可以通过 `openvpn` 来连接内部集群

## 下一步

此时我们访问 `whoami.docker.localhost` 仍然是通过指定 Host 的方式

``` bash
$ curl -H Host:whoami.docker.localhost http://172.18.0.1
```

那我们为什么不能直接 `curl whoami.docker.localhost` 来访问服务呢？

因为 DNS 无法解析到正确的地址

那我们使用 IP 地址又不是很方便，这时应该怎么办？

**搭建一个本地的DNS服务**，下一章将介绍如何搭建一个本地的DNS服务。
