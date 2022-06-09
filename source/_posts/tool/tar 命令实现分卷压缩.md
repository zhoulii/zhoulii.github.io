---
title: tar 命令实现分卷压缩
date: 2020-08-14 22:00:00
updated: 2022-06-09 22:00:00
tags: ["linux"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.压缩

```shell
tar -czvf - jdk1.8.0_221/ | split -b 100m - jdk1.8.0_221.tar.gz
```

## 02.解压

```shell
cat jdk1.8.0_221.tar.gza* | tar -xzv
```
