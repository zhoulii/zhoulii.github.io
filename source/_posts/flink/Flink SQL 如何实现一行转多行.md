---
title: Flink SQL 如何实现一行转多行
date: 2020-04-12 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.问题描述

用户的数据记录中包含多个字段，并且某个字段的内容是一个结构复杂的 `json`，现在要提取 `json` 中的数据，与其他字段组成一条或多条记录输出。

## 02.如何解决

Flink 中的 UDTF 可以实现一行数据到多行数据的转换，下面以《[Flink SQL 中TableFunction使用分析](https://baijiahao.baidu.com/s?id=1651693041743099274&wfr=spider&for=pc)》中的示例来演示下如何自定义 UDTF。假设输入数据格式如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-udtf-1.png)

要求通过 flink sql 输出如下格式数据：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-udtf-2.png)

此时 UDTF 的实现为：

```JAVA
public class MyUDTF extends TableFunction<Row> {
    @FunctionHint(output = @DataTypeHint("Row<type String, value String>"))
    public void eval(String s) {
        final JSONArray jsonArray = JSONArray.parseArray(s);
        for(int i = 0; i < jsonArray.size(); i++) {
            final JSONObject jsonObject = jsonArray.getJSONObject(i);
            final String type = jsonObject.getString("type");
            final String value = jsonObject.getString("value");
            collect(Row.of(type, value));
        }
    }
}
```

下面给出了这个 UDTF 的测试用例：

```java
public static void main(String[] args) {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

    EnvironmentSettings settings = EnvironmentSettings
            .newInstance()
            .inStreamingMode()
            .useBlinkPlanner()
            .build();

    StreamTableEnvironment tEnv = StreamTableEnvironment.create(env, settings);

    List<Row> sourceData = Collections.singletonList(Row.of(
            "dev01",
            "1574944573000",
            "[{\"type\":\"temperature\",\"value\":\"10\"}," +
                    "{\"type\":\"battery\",\"value\":\"1\"}]"));

    DataStreamSource<Row> rowDataStreamSource = env.fromCollection(sourceData);
    Table table = tEnv.fromDataStream(rowDataStreamSource, $("devid"), $("time"), $("data"));
    tEnv.createTemporaryView("source", table);
    tEnv.createTemporarySystemFunction("my_udtf", MyUDTF.class);

    TableResult tableResult = tEnv.executeSql("select `devid`, `time`, `type`, `value` " +
            "from source, lateral table(my_udtf(data)) as t(`type`,`value`)");
    tableResult.print();
}
```