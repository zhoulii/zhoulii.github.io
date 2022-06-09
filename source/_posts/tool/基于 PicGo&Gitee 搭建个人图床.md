---
title: 基于 PicGo&Gitee 搭建个人图床
date: 2019-01-15 22:00:00
updated: 2022-06-09 22:00:00
tags: ["picgo"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.Gitee 相关配置

首先要在 Gitee 上创建一个仓库，然后就是生成私人令牌。在 Gitee 的设置页面可以找到私人令牌标签，按下图所示进行配置，记住生成的私人令牌。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/create_personal_access_token_for_picgo.png)

## 02.PicGo 相关配置

下载安装 [PicGo](https://github.com/Molunerfinn/PicGo/releases)，然后将镜像地址设置为 `https://registry.npm.taobao.org/`。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/set_the_mirror_repository_for_picgo_plugin_1.png)

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/set_the_mirror_repository_for_picgo_plugin_2.png)

打开插件设置栏，搜索安装 Gitee 插件（*如果没有 nodejs 环境，那么需要先安装 nodejs，然后重启 PicGo*） 。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/install_gitee_plugin_for_picgo.png)

接下来就是配置 Gitee 图床，配置参数含义可参照下图注解。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/config_gitee_plugin_for_pico.png)

## 03.上传区及相册

完成上述操作之后，即可在 PicGo 的上传区上传图片。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/upload_image_to_gitee_by_picgo.png)

在相册栏可以查看已上传的图片，还可以复制链接、修改链接、删除图片以及进行一些批量操作。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/album_in_picgo.png)

