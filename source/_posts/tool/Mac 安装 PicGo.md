---
title: Mac 安装 PicGo
date: 2019-01-15 22:00:00
updated: 2022-06-09 22:00:00
tags: ["picgo"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.安装步骤

下载 [PicoGo](https://github.com/Molunerfinn/PicGo/releases) 的 dmg 包，点击安装。完成后，打开 PicGo 时可能会遇到 `无法打开 PicGo，因为它来自身份不明的开发者` 问题。解决办法如下：

1. 在终端执行如下命令 `sudo spctl --master-disable`；
2. 打开 `系统偏好设置 -> 安全性与隐私 –> 通用`，*允许从以下位置下载的 App* 选择 `任何来源`；

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/solve_problem_encountered_during_picgo_installation_on_mac.png)

## 02.参考资料

- [解决 MacOS 因为它来自身份不明的开发者,不显示允许任何来源 –安装文件下载损坏问题](https://c4dsky.com/26380.html)。

