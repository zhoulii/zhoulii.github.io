---
title: yum 方式安装 MySQL
date: 2016-04-07 22:00:00
updated: 2022-06-09 22:00:00
tags: ["mysql"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.环境说明

- CentOS 7.7.1908
- MySQL 5.7

## 02.安装步骤

检测是否自带：

```bash
rpm -qa | grep mysql
```

卸载自带 mysql：

```bash
rpm -e --nodeps <rpm qa|grep mysql命令查出来的包>
```

下载 mysql 源：

```bash
wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
```

安装 mysql 源：

```bash
rpm -ivh mysql80-community-release-el7-3.noarch.rpm
```

更新 yum 源：

```bash
yum update
```

列出可用版本：

```shell
yum repolist all | grep mysql
```

禁用 yum 源中的 mysql80：

```bash
yum-config-manager --disable mysql80-community
```

开启 yum 源中的 mysql57：

```bash
yum-config-manager --enable mysql57-community
```

验证 enable 的 mysql 版本：

```bash
yum repolist all | grep mysql
```

再次卸载 mysql：

```bash
rpm -e --nodeps <rpm -qa|grep mysql命令查出来的包，不要删带有noarch的包>
```

安装 mysql：

```bash
yum install -y mysql-community-server
```

启动 mysql：

```bash
sudo systemctl start mysqld.service
```

检查 mysql 状态：

```bash
sudo systemctl status mysqld.service
```

设置开机自启：

```bash
sudo systemctl enable mysqld.service
```

获取 'root'@'localhost' 的密码：

```bash
sudo grep 'temporary password' /var/log/mysqld.log
```

连接 mysql 服务：

```bash
mysql -uroot -p
```

修改 root 密码：

```bash
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';
```

## 03.参考资料

- [菜鸟教程](https://www.runoob.com/mysql/mysql-install.html)
- [官方资料](https://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)
- [yum安装指定版本mysql](https://www.cnblogs.com/zhylioooo/p/11084489.html)