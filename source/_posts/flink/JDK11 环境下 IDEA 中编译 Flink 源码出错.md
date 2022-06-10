---
title: JDK11 环境下 IDEA 中编译 Flink 源码出错
date: 2020-04-09 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.问题描述

Flink 源码导入 IDEA 后，执行 `Build -> Build Project` 出错，错误信息为 <i><font color='red'>程序包 sun.misc 不存在</font></i>，使用的 java 版本为 jdk11。

## 02.解决办法

jdk 版本切换为 jdk8 或者按下图所示，取消选中 <i><font color='red'>Use ‘–release’ option for cross-compilation (Java 9 and later)</font></i>

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/release_option_in_idea.png)

## 03.参考资料

- [Importing Flink into an IDE - Common Problems](https://nightlies.apache.org/flink/flink-docs-master/docs/flinkdev/ide_setup/#compilation-fails-with-package-sunmisc-does-not-exist)

