---
title: Flink 使用 FTP 作为 Checkpoint 文件系统存在的问题
date: 2020-07-05 22:00:00
updated: 2022-06-08 22:00:00
tags: ["flink"]
categories:
  - flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.背景说明

{% post_link 'flink/Flink 使用 FTP 作为 Checkpoint 文件系统' %} 介绍了如何让 Flink Checkpoint 后端支持 FTP，并实现了此项功能。项目上线后，FTP 作为 Flink Checkpoint 状态后端可以正常工作，但项目运行一段时间后，偶尔在做 Checkpoint 时会出现端口被占用异常，从而出现 Checkpoint 失败，作业重启的问题。

## 02.问题排查

首先看下异常发生的位置：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/flink-ftp-checkpoint-failed.png)

下面是我的排查思路：

-   检查机器的端口，发现大量端口处于 time_wait 状态，推测短链接建立太多，机器端口被占用完；
-   排查代码，`_serverSocketFactory_.createServerSocket` 绑定的端口是 0，意思是由操作系统分配可用端口，无端口可分配也会抛出端口被占用异常，进一步验证上面猜测；

分析出机器端口被占用完，接下来就是找出 FTPClient 中为什么会有大量端口没被释放的问题了。还是回到 `_openDataConnection_` 方法，在 FTPClient 与 FTP 进行数据交互时，每个数据交互操作都需要调用这个方法，该方法会返回一个与服务端进行数据传输的 Socket，猜想如果这些 Socket 没被关闭，那么就会不断有端口被占用。而跟踪代码可以发现，FTPClient 在用完这些 Socket 后，都及时调用了其 close 方法去关闭连接，所以上面的猜想又进入了死胡同。

在网上查阅相关资料，发现了下面一段描述：`socket.close()` 操作并不能立即释放绑定的端口，而是把端口设置为 TIME_WAIT 状态，过段时间(默认240s)才释放(用 `netstat -na` 可以看到)，如果建立短连接的操作较为频繁，可能会导致系统端口资源耗尽。所以，最终确定问题还是在 Socket 的关闭上，既然找到了问题所在，那么在不修改 FTPClient 代码的条件下，可采用如下解决方案：

-   扩大机器可用的端口范围
-   开启端口的复用与快速回收

运维人员按上述方案对操作系统进行设置之后，checkpoint 数据到 FTP 时，未再发生端口被占用问题。

## 03.参考资料

-   [请教Address already in use: connect问题](https://bbs.csdn.net/topics/320024769)
-   [记录 FTPClient 超时处理的相关问题](https://www.cnblogs.com/dasusu/p/10006899.html)