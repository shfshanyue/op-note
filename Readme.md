# 当我有服务器时我做了什么 · <small>个人服务器运维指南</small>

去年我写了一篇文章: [当我有一台服务器时做了什么](https://shanyue.tech/op/when-server)。当时为了不至于浪费我在阿里云低价优惠买的服务器，于是使用 docker 跑了一个应用，并参照我司的技术架构搭建了相关的基础设施。

**现在仔细想来，这些经验也非常有益于有一台服务器却不知所措的人，于是有了本系列文章，希望能够帮助到那些服务器买来已久却在吃灰的人。** 另外如果你是一个自由开发者，本系列文章或许对你环境搭建也会有些许启发。

<!--more-->

## 目录

1. [序·当我有一台服务器时我做了什么]()
1. [序·当我有一台服务器时我做了什么(2019)]()

### 如果没有服务器 · PaaS

随着 PaaS 的流行， **没有钱没有服务器也可以作很多事情**。如

+ `netlify` 托管网站
+ `github` 托管私有仓库，并结合 `github action` 做 `CI/CD`
+ `quay` 构建镜像
+ `cloudflare` 免费的 CDN
+ `sentry` 异常上报
+ `aws-lambda` 简单的 API

**如果说有什么缺陷的话，那就是因为网络而造成的速度问题了。** 本章目录如下

1. [如果你只想搭一个博客]()
1. [静态网站托管: netlify]()
1. [免费的 API Server 与数据存储]()
1. [申请你的域名邮箱]()
1. [使用 sentry 做异常监控]()

### 服务器初始配置

当我有了服务器时，我应该先在上边做点什么准备工作？

+ ssh-config: 方便很快地进入服务器
+ ssh key: 方便 git 的设置，以及与其它服务器相互 ssh
+ vim/tmux: 方便在 linux 下工作
+ htop/rsync/lsof/git/...: 等基础软件与依赖的配置

**如果使用 `ansible`，能够在三分钟内快速配置完所有前置准备工作。** 本章目录如下

1. [服务器初始登录配置：ssh-config](https://github.com/shfshanyue/op-note/blob/master/init.md)
1. [服务器ssh key 以及 git 的配置](https://github.com/shfshanyue/op-note/blob/master/ssh-setting.md)
1. [系统信息查看相关命令](https://github.com/shfshanyue/op-note/blob/master/system-info.md)
1. [使用 vim 及其配置](https://github.com/shfshanyue/op-note/blob/master/vim-config.md)
1. [窗口复用与 tmux](https://github.com/shfshanyue/op-note/blob/master/tmux-config.md)
1. [openvpn 配置与内网安全](https://github.com/shfshanyue/op-note/blob/master/vpn-config.md)

### 自动化运维

在做服务器初始的准备工作中，如果只有一台服务器就很简单，但是有了多台就需要考虑一下自动化运维了

1. [使用 ansible 做自动化运维](https://github.com/shfshanyue/op-note/blob/master/ansible-guide.md)
1. [ansible 中的细节问题](https://github.com/shfshanyue/op-note/blob/master/ansible-problem.md)

### docker 与应用开发

当服务器的准备工作结束后，就可以使用它部署一些服务或者简单的应用了: 

+ 一个简单的静态博客，能够了解前端部署的大致流程
+ postgres/redis，做一些测试，存一些有用的数据
+ 一个有状态的后端应用，并用 docker 部署，简单了解后端部署以及前后端配合的大致流程
+ nginx 的反向代理
+ https 证书

如果你刚刚接触服务器，我建议你学习并实践下 docker，不管你是一个前端还是后端都会大有裨益，目录如下

1. [如何使用 docker 高效部署前端](https://github.com/shfshanyue/op-note/blob/master/deploy-fe-with-docker.md)
1. [部署异常监控服务 Sentry](https://github.com/shfshanyue/op-note/blob/master/deploy-sentry.md)

### kubernetes 与应用开发

如果你有多台服务器，建议尽可能搭建一个 k8s cluster。结合 `kubernetes` 与 `helm`，部署也变得相当简单了

1. [搭建一个 k8s 集群]()
1. [部署 postgres]()

### 监控

1. [linux 各项监控指标](https://github.com/shfshanyue/op-note/blob/master/linux-monitor.md)
1. [linux 监控与报警]()

### 高频 linux 命令

记录下来备忘

1. [sed](https://github.com/shfshanyue/op-note/blob/master/linux-sed.md)
1. [awk](https://github.com/shfshanyue/op-note/blob/master/linux-awk.md)
1. [jq](https://github.com/shfshanyue/op-note/blob/master/jq.md)
1. [iptables](https://github.com/shfshanyue/op-note/blob/master/iptables.md)
1. [htop](https://github.com/shfshanyue/op-note/blob/master/htop.md)

