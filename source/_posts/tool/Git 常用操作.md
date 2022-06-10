---
title: Git 常用操作
date: 2018-05-06 22:00:00
updated: 2022-06-09 22:00:00
tags: ["git"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.git log 格式化输出

```SHELL
git log --pretty=oneline --all --graph --abbrev-commit
```

## 02.追查真凶

```SHELL
git blame FileName
```

## 03.删除 untracked 文件

```shell
git clean -df
```

## 04.删除本地分支

```shell
git branch -D BranchName
```

## 05.救命稻草

```shell
git reflog
```

## 06.同步 Gitee 代码到 Github

以 Apache Zeppelin 项目为例，说明如何从 Gitee 上下载代码，并将代码同步到 Github 上。首先，把 Apache Zeppelin 项目 Fork 到自己的 Github 仓库。然后，克隆 Gitee 上的 Apache Zeppelin 项目代码到本地。接着，更换仓库的 origin 与 upstream 地址：

```shell
git remote remove origin
git remote add origin https://github.com/zhoulii/zeppelin.git
git remote add upstream https://github.com/apache/zeppelin.git
```

最后，同步 upstream 仓库代码：

```shell
git fetch upstream master
git rebase upstream/master
git push origin master
```

## 07.将 upstream 中的某个标签同步到 origin

```shell
# 先把标签仓库拉到本地
git fetch upstream tag release-1.12.1
# 推送到远程仓库
git push origin tag release-1.12.1
```

