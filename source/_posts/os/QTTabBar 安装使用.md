---
title: QTTabBar 安装使用
date: 2021-07-01 22:00:00
updated: 2022-06-09 22:00:00
tags: ["windows"]
categories:
  - os
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 前置条件

需要安装 .net3.5，win10 安装 .net3.5 的方法可参考 [解决win10系统无法安装.NET Framework 3.5](https://jingyan.baidu.com/article/455a9950dbe21de167277823.html)。

## 下载安装

国内开发者在 Github 上维护了一个 [QTTabBar](https://github.com/indiff/qttabbar) 的汉化版本，下载安装即可，安装过程比较慢，耐心等待即可。

## 相关配置

QTTabBar 启用后，资源管理器里面有一些网格线，看着不舒服，去掉这些网格线的方法如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/remove_grid_lines_caused_by_qttabbar.png)

在标签栏有一个蓝色的加号按钮，看着也不习惯，去掉的方法如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/remove_blue_button_on_tab_bar.png)

## 其他问题

没有安装 .net3.5 的机器安装 QTTabBar 后，在启用时会闪退。此时，并不是说把 .net3.5 装上，QTTabBar 就能用了，需要先卸载 QTTabBar 并删除一些注册信息，然后再重新安装 QTTabBar，具体的操作可参考 [打开选项闪退的解决方案修订版](https://gitee.com/qwop/qttabbar/attach_files/581136/download)。

