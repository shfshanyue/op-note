---
title: 服务器登录配置
date: 2019-10-10 23:00
keywords: 云服务器登录,ssh-config,ssh免密登录,ssh及服务器登录配置,ssh禁止密码登录
description: 当刚拥有服务器后，首先需要登录服务器，本节主要有以下三个实践操作：快速登录，免密登录，禁用密码。

---

# 服务器登录配置

当你刚拥有一个服务器后，首先需要登录服务器，本节主要有以下三个实践操作：

1. 快速登录: 配置客户端 ssh-config
1. 免密登录: 配置 public key
1. 禁用密码：配置服务器 ssh-config

> 你对流程熟悉后，只需要一分钟便可以操作完成

<!--more-->

+ 原文地址: [云服务器初始登录配置](https://shanyue.tech/op/init)
+ 系列文章: [服务器运维笔记](https://shanyue.tech/op)

# 服务器登录配置

## 快速登录：ssh-config

在本地客户端环境 (MAC) 上配置 ssh-config，使其更方便地访问云服务器

+ `/etc/ssh/ssh_config`
+ `~/.ssh/config`

以下是快速登录我两个服务器 `shanyue` 和 `shuifeng` 的配置

``` config
# 修改 ssh 配置文件 ~/.ssh/config
Host shanyue
    HostName 59.110.216.155
    # HostName 172.17.68.39 私网IP
    User root
Host shuifeng
    HostName <PUBLIC_IP>
    # HostName 172.17.68.40 私网IP
    User root
```

``` shell
# 配置成功之后直接 ssh Host 名称就可以
$ ssh shanyue
The authenticity of host '59.110.216.155 (59.110.216.155)' can't be established.
ECDSA key fingerprint is SHA256:WXULVpZcrX6kENrR5GH0mqRi49Djj22UXba0dRXCVKo.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '59.110.216.155' (ECDSA) to the list of known hosts.

Welcome to Alibaba Cloud Elastic Compute Service !

[root@shanyue ~]#
[root@shanyue ~]#
[root@shanyue ~]#
```

## 免密登录：public-key 与 ssh-copy-id

**如何实现远程服务器的免密登录?**

两个文件: 本地环境的 `~/.ssh/id_rsa.pub` 与 远程服务器的 `~/.ssh/authorized_keys`
一个动作：把本地文件中的内容复制粘贴到远程服务器中

> 如果 ~/.ssh/id_rsa.pub 文件不存在，请参考下一章节使用 ssh-keygen 生成 ssh keys: [ssh key 及 git 配置](https://shanyue.tech/op/ssh-setting)

**即把自己的公钥放在远程服务器**

> 如果不存在文件 `~/.ssh/id_rsa.pub`，则参考下一节使用 `ssh keygen` 生成

简单来说，就是 `Ctrl-C` 与 `Ctrl-V` 操作，不过具体实施起来较为琐碎。**更为重要的是对于新人还有一个门槛：vim 的使用**

此时就需要一个解决生产力的命令行工具应运而生: `ssh-copy-id`

``` ssh
# 在本地环境进行操作

# 会提示你输入密码，成功之后可以直接 ssh 进去
$ ssh-copy-id shanyue

```

## 禁用密码登录

修改云服务器的 ssh 配置文件：`/etc/ssh/ssh_config`。`PasswordAuthentication` 设置为 `no`，禁用密码登录

``` config
# 禁用密码登录
Host *
  PasswordAuthentication no
```
