---
title: Flink 1.11.0 维表 join 条件中包含 UDF 时执行出错
date: 2020-07-12 22:00:00
updated: 2022-06-09 22:00:00
tags: ["Flink"]
categories:
  - Flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.背景

当 flink 1.11.0 执行如下 sql 时报错：

```SQL
select t1.id, t1.product, t2.price from table1 as t1
join table2 for system_time as of t1.proctime as t2
on t1.id = t2.id where current_date = t2.sell_date
```

## 02.排查

出错位置位于 `CommonLookupJoin#extractConstantField`，原因是 RexCall 类型匹配不上，可以在该方法的 match 语句中增加 `case _ => return` 来解决该问题。

## 03.补充

[FLINK-18212](https://issues.apache.org/jira/browse/FLINK-18212) 已经提出并解决了该问题