---
title: Flink 1.11.0 连接 postgre 的问题
date: 2020-10-27 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.问题描述

pg 中有一张 pg_sink 表，结构如下：

| 字段 | 类型 |
| --- | --- |
| Name | VARCHAR |
| address | VARCHAR |
| work | VARCHAR |

使用 Flink 1.11.0 执行如下 SQL：

```sql
create table pg_sink (
	Name VARCHAR,
	address VARCHAR,
	work VARCHAR
) with (
    'connector' = 'jdbc',
    'url' = 'jdbc:postgresql://***:***/***',
    'table-name' = 'pg_sink',
	...
);

create table kafka_source(
    Name VARCHAR,
    address VARCHAR,
    work VARCHAR
) with (
    'connector.type' = 'kafka',
    'format.type' = 'json',
	...
);

insert into pg_sink 
select * from kafka_source;
```

上述 SQL 任务运行失败，错误信息如下：

```shell
Caused by: org.postgresql.util.PSQLException: ERROR: column "Name" of relation "pg_sink" does not exist
...
```

## 02.原因分析

参考《[PostgreSQL 对字段大小写敏感](https://www.cnblogs.com/kaituorensheng/p/5445696.html)》，《[PostgreSQL 中的单引号与双引号](https://blog.csdn.net/LittleGlassHeart/article/details/102566867)》

## 03.如何解决

`org.apache.flink.connector.jdbc.dialect.JdbcDialect#quoteIdentifier` 方法会对 SQL 中的字段加上双引号，而 `org.apache.flink.connector.jdbc.dialect.PostgresDialect#quoteIdentifier` 方法则不会对字段做出处理。所以，只需删除 `PostgresDialect#quoteIdentifier` 方法，让 `JdbcDialect#quoteIdentifier` 方法生效即可解决上述问题。

## 04.补充说明

这种处理方式会引起其他问题，举例解释下：

- schemax.tablex： Schema 名为 schemax，表名为 tablex 
- "schemax.tablex"：表名为 schemax.tablex

所以，如果给标识符直接加上双引号，就没法区分 PG 的 Schema 了。关于该问题的更多讨论可参见 [FLINK-19829](https://issues.apache.org/jira/browse/FLINK-19829)、[FLINK-18640](https://issues.apache.org/jira/browse/FLINK-18640)。

