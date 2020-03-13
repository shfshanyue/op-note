# git 安装及基本配置

`git` 基本上来说是开发者必备工具了，在服务器里没有 `git` 实在不太能说得过去。何况，没有 `git` 的话，**面向github编程** 从何说起，如同一个程序员断了左膀右臂。

> 你对流程熟悉后，只需要一分钟便可以操作完成

<!--more-->

+ 原文地址: [服务器 ssh key 以及 git 的配置](https://github.com/shfshanyue/op-note/blob/master/git.md)
+ 系列文章: [服务器运维笔记](https://github.com/shfshanyue/op-note)

## 安装

``` bash
$ yum install git
```

如果使用 `yum` 来安装 `git` 的话，那实在没有必要单开一篇文章了。那使用 `yum` 的弊端在哪里？我们知道，`yum` 为了保证它的仓库的稳定性，往往软件的版本都会很老。

**而用 `yum` 安装的 `git` 没有语法高亮！**

## 使用 ansible 安装

> 如果你对 ansible 不够了解，可以参考我的文章 [ansible 入门指南](https://mp.weixin.qq.com/s/t2fpzPJk3pCK3qBgo_SdyQ)

选择一个好用的 `Ansible Role` 就可以了，我们选择 [geerlingguy.git](https://github.com/geerlingguy/ansible-role-git)，使用以下命令安装 `Role`

``` bash
$ ansible-galaxy install geerlingguy.git
```

配置 playbook，指定变量，从源码安装，并安装最新版本。

``` yaml
hosts: dev
  roles:
  - role: geerlingguy.git
    vars:
      # 从源码安装
      git_install_from_source: true
      # 安装最新版本
      git_install_from_source_force_update: true
```

使用 `ansible-playbook` 对服务器进行批量安装

``` bash
$ ansible-playbook -i hosts git.yaml
```

## 安装成功

`git version`，查看版本号，此时为 `2.16.2`

``` bash
$ git version
git version 2.16.2
```

再用它 `git status`，查看下语法高亮效果

![git 高亮效果](./assets/git.jpg)

## 配置

全局配置邮箱及用户名，此时就可以愉快地在服务器中使用 `git` 管理代码了

``` bash
$ git config --global user.name shfshanyue
$ git config --global user.email xianger94@gmail.com
```

## 面向 github 编程

但是现在就可以面向 `github` 编程了吗？不！

使用 `ssh -T` 测试连通性

``` bash
$ ssh -T git@github.com
Permission denied (publickey).
```

此时需要配置 `ssh key` 来保证正确地面向github编程，可以查看下篇文章 [ssh key 及 github 配置](https://github.com/shfshanyue/op-note/blob/master/ssh-setting.md)
