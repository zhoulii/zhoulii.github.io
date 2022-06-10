---
title: CentOS 7 中安装 Gitlab
date: 2021-12-12 22:00:00
updated: 2022-06-09 22:00:00
tags: ["git"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.安装必要依赖

```shell
yum install -y curl policycoreutils-python openssh-server perl
```

>在 centos:centos7 容器中安装时，缺少 which 命令，所以最好提前检查下，缺少则安装 `yum install -y which`

## 02.开启 SSH 服务

```SHELL
systemctl enable sshd
systemctl start sshd
```

## 03.配置防火墙

首先使用 `systemctl status firewalld` 查看防火墙是否开启，若没启用，则无需任何操作，若启用了防火墙，则添加如下配置：

```SHELL
firewall-cmd --permanent --add-service=http
firewall-cmd --permanent --add-service=https
systemctl reload firewalld
```

## 04.下载安装 Gitlab

下载地址为 [gitlab/gitlab-ce](https://packages.gitlab.com/gitlab/gitlab-ce) ，如果下载速度较慢，可以去国内镜像源下载，比如说 [清华镜像](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/?C=M&O=D)。下载完成后，执行如下命令进行安装：

```SHELL
rpm -ivh gitlab-ce-14.7.6-ce.0.el7.x86_64.rpm
```

>***卸载 Gitlab***
>
>- `rpm -qa | grep gitlab`
>- `rpm -e --nodeps gitlab-ce-14.7.6-ce.0.el7.x86_64`

## 05.修改配置文件

Gitlab 配置文件位于 `/etc/gitlab/gitlab.rb` ，最小化修改如下：

```RUBY
external_url 'http://172.17.0.2'
```

## 06.启动 Gitlab

使用 `gitlab-ctl start` 启动 Gitlab，如果修改了配置，则使用 `gitlab-ctl reconfigure` 启动 Gitlab。

>修改配置后，启动过程中出现如下错误：`Error executing action `run` on resource 'execute[reload all sysctl conf]'`  。可能时内存不足的原因，关闭部分程序，再次执行，未报错。

启动完成后，使用 `gitlab-ctl status` 查看 gitlab 状态，如果一切正常，则可访问 gitlab 的 web 页面，端口默认为 80，可在配置文件中修改。

在 web 页面中，可使用 root 用户登录，root 用户的初始密码可通过 `cat /etc/gitlab/initial_root_password` 查看。需要注意的时 `initial_root_password` 文件会在 reconfigure 后的 24H 后自动删除，所以登录后，尽快修改 root 密码。

## 07.其他一些命令

- *gitlab-ctl restart*：重启 gitlab
- *gitlab-ctl stop*：关闭 gitlab

## 08.相关参考资料

- [gitlab安装部署以及基本使用](https://blog.csdn.net/hujianlaile/article/details/117200478)
- [GitLab 安装部署使用](https://blog.csdn.net/qq_33921750/article/details/122315123)
- [Download and install GitLab | GitLab](https://about.gitlab.com/install/#centos-7)
- [Index of /gitlab-ce/ | 清华大学开源软件镜像站](https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/?C=M&O=D)
- [GitLab的安装及使用](https://blog.csdn.net/weixin_38610500/article/details/116904413)
- [手把手教你 GitLab 的安装及使用](https://www.jianshu.com/p/b04356e014fa)
- [docker 安装gitlab](https://www.jianshu.com/p/0b86f9021c29)
- [GitLab的安装部署](https://zhuanlan.zhihu.com/p/452106697)

