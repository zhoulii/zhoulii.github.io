---
title: Kafka 单点部署
date: 2015-11-15 22:00:00
updated: 2022-06-09 22:00:00
tags: ["kakfa"]
categories:
  - kakfa
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.配置并启动 zk

进入 conf 目录，将 zoo_sample.cfg 重命名为 zoo.cfg，配置 zoo.cfg 中的 dataDir，然后启动 zk：

```shell
bin/zkServer.sh start
```

## 02.配置并启动 kafka

配置 server.properties 中的 log.dirs，然后启动 kafka：

```shell
bin/kafka-server-start.sh -daemon config/server.properties
```

## 03.topic 相关命令

- 创建 topic

```shell
bin/kafka-topics.sh \
--zookeeper localhost:2181 \
--create \
--topic model_topic \
--partitions 3 \
--replication-factor 1
```

- 查看 topic

```shell
bin/kafka-topics.sh \
--zookeeper localhost:2181 \
--list
```

