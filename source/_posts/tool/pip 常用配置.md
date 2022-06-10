---
title: pip 常用配置
date: 2015-06-16 22:00:00
updated: 2022-06-09 22:00:00
tags: ["pip"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.CentOS 7 中 pip 配置国内镜像源

修改 `~/.pip/pip.conf`（没有则手动创建），内容如下：

```shell
[global]
index-url=http://pypi.douban.com/simple
trusted-host = pypi.douban.com
```

## 02.CentOS 7 中为 python 2.7.5 安装 pip 模块

安装 setuptools：

```shell
curl https://bootstrap.pypa.io/ez_setup.py | python
```

pip 安装模块的时候可能会用到 gcc，所以最好把 gcc 装上：

```shell
sudo yum install -y gcc
```

下载 pip 模块：

```shell
wget https://files.pythonhosted.org/packages/c8/89/ad7f27938e59db1f0f55ce214087460f65048626e2226531ba6cb6da15f0/pip-19.0.1.tar.gz
```

解压 pip 模块：

```shell
tar -zxvf pip-19.0.1.tar.gz
```

安装 pip 模块：

```shell
python setup.py install
```

查看 pip 版本：

```shell
pip --version
```

## 03.CentOS 7 中 pip 安装 matplotlib

安装命令如下：

```shell
pip install matplotlib
```

安装过程中，可能会碰到下面两个问题：

- 问题：`configure: error: no acceptable C compiler found in $PATH`
- 解决：`sudo yum install -y gcc`
- 问题：`_posixsubprocess.c:16:20: fatal error: Python.h: No such file or directory`
- 解决：`sudo yum -y install python-devel`

## 04.参考资料

-   [python安装pip模块](https://www.cnblogs.com/supermanwx/p/10320029.html)
-   [菜鸟教程：Python pip 安装与使用](https://www.runoob.com/w3cnote/python-pip-install-usage.html)