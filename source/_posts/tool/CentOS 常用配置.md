---
title: CentOS 常用配置
date: 2015-06-16 22:00:00
updated: 2022-06-09 22:00:00
tags: ["centos"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.关闭防火墙

查看防火墙状态：

```shell
systemctl status firewalld.service
```

关闭防火墙：

```shell
systemctl stop firewalld.service
```

关闭防火墙开机自启：

```shell
systemctl disable firewalld.service
```

## 02.配置 DNS 服务器

配置 `/etc/resolv.conf`：

```shell
nameserver 114.114.114.114
```

## 03.配置国内 yum 源

进入 [阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/)，点击 centos，进入镜像使用的详情页，按照页面的指导进行相应配置。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/aliyun_mirror.png)

例如，为 CentOS 7 配置阿里云的 yum 源，具体操作如下：

```shell
# 备份数据
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
# 下载配置
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
# 生成缓存
yum makecache
```

## 04.配置机器之间的 SSH 免密登录

生成密钥：

```shell
# 如果A需要SSH免密登录到其他机器，那么在A上执行下面的命令
ssh-keygen -t rsa
```

授权：

```shell
# 如果A需要SSH免密登录B，那么在A上执行下面的命令
ssh-copy-id B
```

测试：

```shell
# 在A上执行下面的命令
ssh B
```

> 如果 ssh-copy-id 命令不存在，则需要执行下面命令安装 openssh-clients：
>
> ```shell
> # 注意：涉及免密登录的每台机器都得安装openssh-clients
> sudo yum install -y openssh-clients
> ```