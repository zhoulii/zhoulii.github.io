---
title: Flink 远程调试
date: 2017-10-12 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.调试 JobManager

在 flink-conf.yaml 中加入如下配置：

```bash
# jdk8
env.java.opts.jobmanager: -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=9750
# jdk11
env.java.opts.jobmanager: -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:9750
```


## 02.调试 TaskManager

在 flink-conf.yaml 中加入如下配置：

```bash
# jdk8
env.java.opts.taskmanager: -agentlib:jdwp=transport=dt_socket,server=y,suspend=y,address=9751
# jdk11
env.java.opts.taskmanager: -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:9751
```



