---
title: Jupyter Lab 的安装与配置
date: 2021-03-08 22:00:00
updated: 2022-06-09 22:00:00
tags: ["jupyter"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.安装 jupyterlab

```shell
pip install jupyterlab
```

## 02.生成配置文件

```shell
jupyter lab --generate-config
```

## 03.生成登录密码

```shell
>> ipython
Python 3.7.10 (default, Feb 26 2021, 18:47:35)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.22.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from notebook.auth import passwd

In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'argon2:$argon2id$v=19$m=10240,t=10,p=8$c/W2UuRTJI1k4o40ygdcSA$/GAGaIdbluH45S2LWbrMOA'
```

## 04.修改配置文件

打开配置文件：

```shell
vim ~/.jupyter/jupyter_lab_config.py
```

修改如下配置：

```shell
c.ServerApp.root_dir = '/opt/notebook'
c.ServerApp.allow_root = True
c.ServerApp.ip = '*'
c.ServerApp.open_browser = False
c.ServerApp.password_required = True
c.ServerApp.password = 'argon2:$argon2id$v=19$m=10240,t=10,p=8$c/W2UuRTJI1k4o40ygdcSA$/GAGaIdbluH45S2LWbrMOA'
```

## 05.启动 jupyterlab

```shell
jupyter lab
```