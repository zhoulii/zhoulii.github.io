---
title: Flink 1.11 新特性：Application 部署模式
date: 2020-05-11 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.异常案例

先来看一个例子：

```JAVA
final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
EnvironmentSettings bsSettings = EnvironmentSettings.newInstance().useBlinkPlanner().inStreamingMode().build();
StreamTableEnvironment bsTableEnv = StreamTableEnvironment.create(env, bsSettings);
bsTableEnv.executeSql("create table pt (tfield timestamp) with ( 'connector' = 'print')");
bsTableEnv.executeSql("insert into pt values (1611884521000)");
```

执行上述代码时会发生如下异常：

```shell
Exception in thread "main" org.apache.flink.table.api.ValidationException: Column types of query result and sink for registered table 'default_catalog.default_database.pt' do not match.
Cause: Incompatible types for sink column 'tfield' at position 0.

Query schema: [EXPR$0: BIGINT NOT NULL]
Sink schema:  [tfield: TIMESTAMP(6)]
......
```

异常信息提示的很明确，问题原因是字段类型不匹配。

## 02.原因说明

上述案例之所以会发生字段类型不匹配，原因在于 Flink 中对 Timestamp 的定义。与一般的认知不同，Flink 中的 Timestamp 不是一个 13 位的长整形数字，而是一个 **2021-01-29T09:42:01** 样式的日期。所以在使用 Flink SQL 时，直接将长整形的字段类型定义为 Timestamp 会发生异常。

## 03.正确案例

在 Flink 中，如果想把 13 位的长整形数字当做时间戳来用的话，需要先将其转换为 Flink 中的时间戳，代码如下：

```JAVA
final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
EnvironmentSettings bsSettings = EnvironmentSettings.newInstance().useBlinkPlanner().inStreamingMode().build();
StreamTableEnvironment bsTableEnv = StreamTableEnvironment.create(env, bsSettings);

bsTableEnv.executeSql("create table pt (tfield timestamp) with ( 'connector' = 'print')");
bsTableEnv.executeSql("insert into pt values (TO_TIMESTAMP(FROM_UNIXTIME(1611884521000 / 1000, 'yyyy-MM-dd HH:mm:ss')))");
```

而如果需要在 DDL 中完成上述操作，可以先将原始 13 位数字字段定义为 BIGINT，然后使用计算列将其转换为 Timestamp，示例代码如下：

```SQL
create table t1 (
	long_ts_field BIGINT,
    ts_field as TO_TIMESTAMP(FROM_UNIXTIME(long_ts_field / 1000, 'yyyy-MM-dd HH:mm:ss')),
    ...
) with (
	...
)
```

## 04.了解更多

如果想了解更多与 Flink 时间戳有关的知识，可参考如下链接：[《Flink SQL中Timestamp使用的坑》](https://blog.csdn.net/zhangdongan1991/article/details/105796613)、[《Flink 中 Date 和 Time 类型介绍》](https://nightlies.apache.org/flink/flink-docs-release-1.11/dev/table/types.html#date-and-time)；如果想了解 Flink 内置的时间转换函数，可参考如下链接：[《Flink Temporal Functions》](https://nightlies.apache.org/flink/flink-docs-release-1.11/dev/table/functions/systemFunctions.html#temporal-functions)；