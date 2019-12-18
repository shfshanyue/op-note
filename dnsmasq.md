# 搭建集群内部 DNS 服务器

当我们使用 `traefik` 反向代理和自动服务发现后，我们对集群内部的服务分为两类

1. 私有服务。如 `gitlab`，`traefik Dashboard`，`redis`，`postgres` 以及自己实现的不公开的私有服务
1. 公有服务。如我的博客，网站，以及为它们提供服务的 API
