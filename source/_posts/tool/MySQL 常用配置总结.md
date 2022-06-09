---
title: MySQL 常用配置总结
date: 2016-04-07 22:00:00
updated: 2022-06-09 22:00:00
tags: ["mysql"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.解除 MySQL 对简单密码的限制

在 `/etc/my.cnf` 的 <i><font color=red>[mysqld]</font></i> 标签下添加如下配置，然后重启 MySQL 服务即可：

```bash
plugin-load=validate_password.so 
validate-password=OFF
```

## 02.MySQL 5.7 开启 binlog

在 `/etc/my.cnf` 的 <i><font color=red>[mysqld]</font></i> 标签下添加如下配置，然后重启 MySQL 服务即可：

```shell
server_id=1			# 配置 MySQL Replication 时需要定义
log_bin=mysql-bin	# 开启 binlog
binlog_format=ROW 	# 选择 ROW 模式
expire_logs_days=30	# 过期时间
```

## 03.参考资料

- [yum 安装指定版本 MySQL](https://www.cnblogs.com/zhylioooo/p/11084489.html)

