---
title: Ubuntu 20.04 设置国内镜像源
date: 2021-07-01 22:00:00
updated: 2022-06-09 22:00:00
tags: ["ubuntu"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.获取配置

点击 [Ubuntu 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/) ，选择合适的版本，获取到相应的配置，接下来就是修改 Ubuntu 的软件源。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/ubuntu_source_supported_by_tsinghua.png)

## 02.修改配置

首先，进行备份：

```shell
sudo cp /etc/apt/sources.list /etc/apt/sources.list.back
```

然后，替换 source.list 内容：

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

## 03.更新索引

更新包索引：

```shell
sudo apt-get update
```

该步骤出现了如下所示的证书异常：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/apt_get_update_error.png)

解决办法也很简单，只需将源地址中的 https 修改为 http。替换 source.list 内容：

```shell
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

重新执行更新包索引命令，此时操作顺利完成。

## 04.升级软件

更新完包索引后，可以执行如下命令来升级软件：

```shell
sudo apt-get upgrade
```

## 05.测试验证

最后，来安装一个软件，这个软件名称为 build-essential，它可以配置好 Ubuntu 上的 C/C++ 的编译环境：

```shell
sudo apt install build-essential
```

安装操作可正常完成，无异常发生。