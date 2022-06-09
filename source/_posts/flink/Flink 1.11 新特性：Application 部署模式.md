---
title: Flink 1.11 新特性：Application 部署模式
date: 2020-10-11 22:00:00
updated: 2022-06-09 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

Flink 1.11 新增了一种新的应用部署模式：application mode，下面我们来了解下这种部署模式解决了什么问题以及是如何实现的。

## 01.当前的应用部署模式

在 1.11 版本之前，Flink 支持两种部署模式：session 和 per-job，它们的区别在于集群的生命周期和应用的资源隔离程度不同。

**session mode**

session 模式假定已经存在了一个 Flink 集群，所有的作业都提交到这个集群上，作业间共用并竞争这个集群的资源。在 session 模式下，用户无需为每个作业分配集群资源，但作业共用 jobmanager 和 taskmanager，如果一个作业导致 taskmanager 宕机，那么这个 taskmanager 上承载的作业都会失败。而大规模的作业失败意味着大规模的作业恢复，作业恢复过程需要访问文件系统，这可能导致文件系统的压力过大。此外，jobmanager 负责记录跟踪作业的状态，启动的作业越多，jobmanager 的负载也会越大。

除了上面提到的问题，session 模式还有一个不易察觉的缺点，那就是查看作业日志较为不方便，究其原因，同样是共用 taskmanager 引起的。

虽然问题较多，但 session 模式还是有其适用用场景的。得益于一个既存集群的优势，session 模式下应用的启动速度较快，所以 Session 模式一般用来部署那些对延迟非常敏感但运行时长较短的作业。

**per-job mode**

per-job 模式下，会使用集群管理框架（如 yarn、kubernetes）为每个作业创建一个独享的 Flink 集群，当作业运行结束，集群资源会被释放。显然，per-job 模式下作业启动的延时会比较大，但是由于作业资源完全隔离，一个作业的失败不会影响其他作业的运行。此外，per-job 模式下，作业不会共享 jobmanager，也就不会发生 session 模式中因作业过多导致 jobmanager 负载大的问题。

结合上述的特点，per-job 模式通常用来部署长时运行且对启动延迟不敏感的作业。

## 02.部署模式存在的问题

无论是提交 session 模式还是 per-job 模式的应用，Flink 应用的 main 方法都是在客户端执行，主要包含如下两个过程：1）将作业翻译成 JobGraph；2）将 JobGraph 及依赖上传到集群中。如果应用逻辑很复杂，步骤 1 则会消耗更多的 CPU 及内存，如果依赖较大，步骤 2 则会消耗更高的带宽。所以，如果多个用户、在同一节点（一般来讲，在生产环境中，是会有一个节点作为应用提交的统一入口）同时提交 Flink 应用，这个提交应用的节点很可能成为瓶颈。下图中的 Deployer 表示应用提交节点，三角形表示提交作业时客户端需要做的工作。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-application-1.png)

为了减轻 Deployer 的负载压力，Flink 社区在 per-job 模式的基础上实现了 application mode。

## 03.Application Mode 实现原理

如下图所示，application mode 将客户端的工作负载转移到了 jobmanager 中（即在 jobmanager 中执行应用 main 方法），客户端仅负责发送作业提交请求，工作负载很轻，即使大量应用同时提交，也不会给 Deployer 造成太大的压力。并且，application mode 会为每一个 application 单独创建一个 Flink 集群，这意味着每一个 application 独享一个 jobmanager，从而 jobmanager 也不会因为承担了部分客户端的工作而造成负载过大。

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-application-2.png)

和 per-job mode 相比，application mode 允许一个 application 中包含多个 job，job 的执行顺序和启动 job 的命令有关。当使用阻塞式的 execute 命令来提交 job 时，job 是顺序执行的，下一个 job 会在上一个 job 执行完成之后才会提交。当使用非阻塞式的 executeAsync 命令来提交 job 时，下一个 job 在上一个 job 提交之后会立即提交，无需等待上个 job 的完成，也就是说多个 job 可以同时运行。

**需要注意一点**：由于 application mode 下，应用的 main 方法在 jobmanager 中执行，所以要保证用户代码访问的外部资源在 jobmanager 中是可以访问到的。 例如，当用户代码使用 registerCachedFile 方法来注册资源时，这个资源要能被 jobmanager 访问。

## 04.进一步减少网络资源

以 application mode on yarn 为例，当提交一个 application mode 的应用时，客户端不仅需要传输用户 jar 到 jobmanager，还需要将 Flink 的二进制包上传到 Yarn ，用于启动 Flink 集群。对客户端来说，这两个传输工作也要占用相当的带宽资源。并且，每次提交应用都上传一份相同的 Flink 二进制包到 Yarn 上不仅浪费带宽，也浪费存储空间，Flink 1.11 正是从由此出发，对 Flink on Yarn 做了如下两点优化：

1.  通过一个远程路径来指定启动 Flink 集群所需的二进制包；
2.  通过一个远程路径来指定应用 jar；

通过第一个优化点，提交应用时就不必重复上传 Flink 依赖了 ，直接从远程路径拉去即可。并且，由于 yarn 的缓存机制，如果之前一个 nodemanager 上启动过 Flink 进程，当再次启动时，Flink 依赖可直接从本地缓存中读取，进一步加快了应用的提交过程。

需要知道的是，上述两点优化并不只针对于 application mode on yarn，而是适用于 Flink on Yarn 中的所有应用提交模式。

## 05.Application Mode on Yarn 示例

```SHELL
./bin/flink run-application -t yarn-application \
-Djobmanager.memory.process.size=2048m \
-Dtaskmanager.memory.process.size=4096m \
-Dyarn.provided.lib.dirs="hdfs://myhdfs/remote-flink-dist-dir" \
hdfs://myhdfs/jars/MyApplication.jar
```

## 06.Application Mode on k8s 示例

```shell
./bin/flink run-application -p 8 -t kubernetes-application \
-Dkubernetes.cluster-id=<ClusterId> \
-Dtaskmanager.memory.process.size=4096m \
-Dkubernetes.taskmanager.cpu=2 \
-Dtaskmanager.numberOfTaskSlots=4 \
-Dkubernetes.container.image=<CustomImageName> \
local:///opt/flink/usrlib/my-flink-job.jar
```

**注意**：在 Flink 1.11 中，当使用 application mode on k8s 时，只能通过 local 协议来指定应用 jar 包，这意味着必须将应用 jar 包打到 Flink 镜像中。

## 07.参考资料

-   [Application Deployment in Flink: Current State and the new Application Mode](https://flink.apache.org/news/2020/07/14/application-mode.html)
-   [Run an application in Application Mode](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/deployment/yarn_setup.html#run-an-application-in-application-mode)
-   [Flink Kubernetes Application](https://ci.apache.org/projects/flink/flink-docs-release-1.11/ops/deployment/native_kubernetes.html#flink-kubernetes-application)
-   [Flink on YARN的第三种部署模式：Application Mode](https://www.jianshu.com/p/90d9f1f24937)
-   [探究 flink1.11 Application 模式](https://zhuanlan.zhihu.com/p/160800648)