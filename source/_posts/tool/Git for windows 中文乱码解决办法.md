---
title: Git for windows 中文乱码解决办法
date: 2016-03-11 22:00:00
updated: 2022-06-09 22:00:00
tags: ["git"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.问题

`git status` 时显示乱码：

```
\316\304\261\276\316\304\265\265.txt
```

## 02.解决

执行如下命令：

```bash
git config --global core.quotepath false
```

## 03.参考

- [Git for windows 中文乱码解决方案](https://www.cnblogs.com/ayseeing/p/4203679.html)
