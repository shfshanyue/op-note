# 当我有服务器时我做了什么 · <small>个人服务器运维指南</small>

去年我写了一篇文章: [当我有一台服务器时做了什么](https://shanyue.tech/op/when-server)。当时为了不至于浪费我在阿里云低价优惠买的服务器，于是使用 docker 跑了一个应用，并参照我司的技术架构搭建了相关的基础设施。

**现在仔细想来，这些经验也非常有益于有一台服务器却不知所措的人，于是有了本系列文章，希望能够帮助到那些服务器买来已久却在吃灰的人。** 另外如果你是一个自由开发者，本系列文章或许对你环境搭建也会有些许启发。

> 如果对你能够有所帮助，可以帮我在 [shfshanyue/op-note](https://github.com/shfshanyue/op-note) 上点个 star。

## 目录

1. 序
    1. [导读](https://github.com/shfshanyue/op-note/blob/master/introduction.md)
    1. [序·当我有一台服务器时我做了什么](https://github.com/shfshanyue/op-note/blob/master/when-server.md) · [(掘金)](https://juejin.im/post/5c9232a8e51d45729b3b71e1)
    1. [序·当我有一台服务器时我做了什么(2019)]() - TODO
    1. [序·个人服务器开发应用架构推荐]()
1. 如果没有服务器
    1. [如果你只想搭一个博客](https://github.com/shfshanyue/op-note/blob/master/if-you-want-a-blog.md) · [(掘金)](https://juejin.im/post/5db78500f265da4d0a68cef7)
    1. [使用 netlify 托管你的前端应用](https://shanue.tech/op/deploy-fe-with-netlify)
    1. [免费的 API Server 与数据存储]() - TODO
    1. [申请你的域名邮箱]() - TODO
    1. [使用 sentry 做异常监控]() - TODO
1. 服务器初始化配置
    1. [服务器初始登录配置：ssh-config](https://github.com/shfshanyue/op-note/blob/master/init.md) · [(掘金)](https://juejin.im/post/5da724506fb9a04e2a73d96c)
    1. [服务器ssh key 以及 git 的配置](https://github.com/shfshanyue/op-note/blob/master/ssh-setting.md) · [(掘金)](https://juejin.im/post/5da7be00e51d45781c6fcd2d)
    1. [系统信息查看相关命令](https://github.com/shfshanyue/op-note/blob/master/system-info.md) · [(掘金)](https://juejin.im/post/5dad7681f265da5bb86ad2f5)
    1. [使用 vim 及其配置](https://github.com/shfshanyue/op-note/blob/master/vim-config.md) · [(掘金)](https://juejin.im/post/5dae491b5188251d2c4ea40d)
    1. [窗口复用与 tmux](https://github.com/shfshanyue/op-note/blob/master/tmux-config.md) · [(掘金)](https://juejin.im/post/5dafabd86fb9a04df00ef01f)
1. 自动化运维
    1. [ansible 简易入门](https://github.com/shfshanyue/op-note/blob/master/ansible-guide.md) · [(掘金)](https://juejin.im/post/5dafb50c6fb9a04e1325f2ff)
    1. [ansible 必知必会](https://github.com/shfshanyue/op-note/blob/master/ansible-problem.md) - TODO
1. 了解 docker 
    1. [docker 简易入门](https://github.com/shfshanyue/op-note/blob/master/docker.md)
    1. [使用 docker 高效部署前端应用](https://github.com/shfshanyue/op-note/blob/master/deploy-fe-with-docker.md)
1. 使用 docker compose 编排应用
    1. [部署异常监控服务 Sentry](https://github.com/shfshanyue/op-note/blob/master/deploy-sentry.md)
1. 使用 kubernetes 编排应用
    1. [搭建一个 k8s 集群](https://github.com/shfshanyue/learn-k8s)
    1. [部署你的第一个应用: Pod，Deployment 与 Service](https://github.com/shfshanyue/learn-k8s/blob/master/pod.md) · [(掘金)](https://juejin.im/post/5db8c2b46fb9a020256692dc)
    1. [通过外部域名访问你的应用: Ingress](https://github.com/shfshanyue/learn-k8s/blob/master/ingress.md) · [(掘金)](https://juejin.im/post/5db8da4b6fb9a0204520b310)
    1. [自动为你的域名添加 https](https://github.com/shfshanyue/learn-k8s/blob/master/https.md) · [(掘金)](https://juejin.im/post/5db8d94be51d4529f73e2833)
    1. [部署利器 Helm 安装及简介](https://github.com/shfshanyue/learn-k8s/blob/master/helm.md)
    1. [github 上的持续集成方案 drone.ci 简介及部署](https://github.com/shfshanyue/op-note/blob/master/deploy-drone.md)
    1. [前端部署发展史](https://github.com/shfshanyue/op-note/blob/master/deploy-fe.md)
1. 监控
    1. [linux 各项监控指标](https://github.com/shfshanyue/op-note/blob/master/linux-monitor.md) · [(掘金)](https://juejin.im/post/5dae57fbf265da5b5b6c7881)
    1. [linux 监控与报警]() - TODO
1. 高频 linux 命令
    1. [sed](https://github.com/shfshanyue/op-note/blob/master/linux-sed.md) · [(掘金)](https://juejin.im/post/5db1053df265da4d57770c30)
    1. [awk](https://github.com/shfshanyue/op-note/blob/master/linux-awk.md)
    1. [jq](https://github.com/shfshanyue/op-note/blob/master/jq.md) · [(掘金)](https://juejin.im/post/5db104f7f265da4d2e121510)
    1. [iptables](https://github.com/shfshanyue/op-note/blob/master/iptables.md)
    1. [tcpdump](https://github.com/shfshanyue/op-note/blob/master/linux-tcpdump.md)
    1. [htop](https://github.com/shfshanyue/op-note/blob/master/htop.md)
    1. [案例: 使用jq与sed制作掘金面试文章榜单](https://github.com/shfshanyue/op-note/blob/master/jq-sed-case.md)

## 关注我

我是山月，我会定期分享全栈文章在个人公众号中。如果你对全栈面试，前端工程化，graphql，devops，个人服务器运维以及微服务感兴趣的话，可以关注我。如果想进群交流，可以添加我微信 shanyue94，备注加群。

![如果你对全栈面试，前端工程化，graphql，devops，个人服务器运维以及微服务感兴趣的话，可以关注我](https://shanyue.tech/qrcode.jpg)
