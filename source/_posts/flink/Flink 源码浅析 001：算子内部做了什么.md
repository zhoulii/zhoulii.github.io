---
title: Flink 源码浅析 001：算子内部做了什么
date: 2018-05-18 22:00:00
updated: 2022-06-08 22:00:00
tags: ["Flink"]
categories:
  - Flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

>从读书到工作，断断续续记过一些笔记，但一直没能坚持下来。总结了几个原因：
>	1.嫌弃笔记的排版不美观~太丑了，写了自己都不想看
>	2.追求笔记的大而全~主题衍生内容繁杂，多到不想写
>	3.懒~显而易见😂
>
>为了能坚持下去，特将本系列文章命名为《浅析》，提醒自己在每篇文章中，尽量用简短的篇幅、简明的语言去大致说清楚一个主题，要更多地关注主干脉络，不要陷在细枝末节中。
>
>闲话少叙，本文我们来看下当调用 Flink 算子的时候，框架内部做了哪些工作。


## 有请 Word Count

`Word Count` 之于大数据正如 `Hello World` 之于编程语言，虽然简单，但执行过程却涉及到底层的诸多方面。下面我们通过一个 `Flink Word Count` 示例来复习下 `Flink` 算子的使用，后文将以 `flatMap` 为例，来探究算子的内部实现。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-word-count.png)

## 相关概念介绍

磨刀不误砍柴功，在探索 `flatMap` 的执行流程之前，先来看下 `Flink` 作业中的一些抽象，具体包括 `Function`、`StreamOperator`、`Transformation`、`DataStream`。

**1.Function**

用户自定义函数的基类，`Word Count` 示例中的 `Tokenizer` 实现了 `FlatMapFunction`，`FlatMapFunction` 便是 `Function` 的一个子类

**2.StreamOperator**

`Flink` 算子在运行时的体现，`AbstractUdfStreamOperator` 是其一个抽象子类，表示包含 `Function` 的 `StreamOperator`

**3.Transformation**

对 `DataStream` 的转换操作，`OneInputTransformation` 是其一个子类，包含 `OneInputStreamOperator`

**4.DataStream**

可以简单将其理解为对 `Transformation` 和 `StreamExecutionEnvironment` 的上层封装，`SingleOutputStreamOperator` 是其一个子类

## flatMap 执行流程

`flatMap` 的调用链如下图所示：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-method-doTransform.png)

**1.获取 `FlatMapFunction` 的返回类型，然后调用带返回类型的重载方法**

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-method-flatmap-1.png)

调用 `flatMap` 方法时，如果没有指定返回类型，那么会通过 `TypeExtractor` 来自动提取返回类型，然后再调用带返回类型的重载方法。

上述代码中有几个有意思的地方有必要了解下：

- `clean(flatMapper)`：闭包清除。简单解释下，内部类持有外部类的引用，如果外部类不可序列化，那么内部类即使实现了序列化接口，也是无法序列化的，闭包清除就是通过字节码技术，将内部类中持有的外部类引用给清除掉，更多信息可参考：[FLINK-1325](https://issues.apache.org/jira/browse/FLINK-1325)；
- `Utils.getCallLocationName()`：获取方法的调用位置，该功能是通过 `Thread#getStackTrace()` 实现的，我们一般很少使用，在框架或工具中用的较多；
- `getFlatMapReturnTypes` 的第四个参数：true 表示当提取类型失败时，返回一个 `MissingTypeInfo` 实例，而不是抛出异常；

2.创建 `StreamFlatMap`，调用 `transform` 方法

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-datastream-flatmap-2.png)

`StreamFlatMap` 是 `AbstractUdfStreamOperator` 的子类，在 `Flink` 作业运行时，每来一条数据，其 `processElement` 方法会被调用一次，内部其实就是调用 `FlatMapFunction` 的 `flatMap` 方法来处理数据。

3.创建 `SimpleOperatorFactory`，调用 `doTransform` 方法

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-method-transform.png)

`SimpleOperatorFactory` 是 `StreamOperator` 的工厂类子类，是对已有 `StreamOperator` 的封装，可以对 `StreamOperator` 做一些额外配置，如设置算子链接策略、定时器服务等。

4.实现用户代码到 `Transformation` 转换的核心逻辑

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-method-doTransform-1.png)

这里进入到创建 `Transformation` 的核心流程：

- 触发异常：前面我们提到过，`TypeExtractor#getFlatMapReturnTypes` 方法提取函数的返回类型时，第四个参数用于控制类型提取失败时是否抛出异常，如果为 true，不抛出异常，而是返回 `MissingTypeInfo`。当前步骤的目的即是用于触发异常，当上一个 `Transformation` 的输出是 `MissingTypeInfo` 时，抛出异常；
- 创建 `OneInputTransformation`：可以发现，`OneInputTransformation` 除了包含 `StreamOperator` 外，还记录了上游 `Transformation`、输出类型、并行度等信息；
- 将 `Transformation` 和 `StreamExecutionEnvironment` 封装为 `DataStream`，这里是 `SingleOutputStreamOperator`；
- 将 `Transformation` 记录到 `StreamExecutionEnvironment`：`StreamExecutionEnvironment` 有个列表类型的成员变量 `transformations`，用于顺序记录 `Flink` 作业中包含的 `Transformation`，后续将遍历该成员变量来创建 `StreamGraph`；
- 返回 DataStream，继续执行转换；

熟悉 `Flink` 的用户应该知道，我们可以调用 `setParallelism` 方法来指定算子的并行度，其方法实现如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-method-setParallelism.png)

可以发现，这里其实是设置  `Transformation`  的并行度。并不是所有的 `Transformation` 都是可以并行的，所以设置之前，会先对并行度的合法性进行校验。

## 总结

用户算子的执行过程，其实就是创建 `Transformation` 的过程，这些 `Transformation` 记录在 `StreamExecutionEnvironment` 中，后续将基于此来创建 `StreamGraph`。