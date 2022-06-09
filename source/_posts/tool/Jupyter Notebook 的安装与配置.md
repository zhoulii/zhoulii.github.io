---
title: Jupyter Notebook 的安装与配置
date: 2020-09-19 22:00:00
updated: 2022-06-09 22:00:00
tags: ["jupyter"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.安装 jupyter notebook

```SHELL
pip install jupyter
```

## 02.生成配置文件

```SHELL
jupyter notebook --generate-config
```

## 03.生成登录密码

```SHELL
>> ipython
Python 3.7.9 (default, Aug 31 2020, 07:22:35)
Type 'copyright', 'credits' or 'license' for more information
IPython 7.19.0 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from notebook.auth import passwd

In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'argon2:$argon2id$v=19$m=10240,t=10,p=8$4sWk2oPuIpqDtIw4MYb/9g$iTpy4dhz3to+3AlyBD7Btw'
```

## 04.修改配置文件

打开配置文件：

```shell
vim ~/.jupyter/jupyter_notebook_config.py
```

修改如下配置：

```SHELL
# 设置 Jupyter 监听的 ip 地址，修改为 * 表示监听所有 ip 地址
c.NotebookApp.ip = '*'
# 设置密码
c.NotebookApp.password = u'argon2:$argon2id$v=19$m=10240,t=10,p=8$4sWk2oPuIpqDtIw4MYb/9g$iTpy4dhz3to+3AlyBD7Btw' 
# 禁止启动时自动打开浏览器
c.NotebookApp.open_browser = False
# 指定端口，默认是8888
c.NotebookApp.port = 8888
# 设置 notebook 文件的目录（要在启动前创建出来）
c.NotebookApp.notebook_dir = '/Users/leo/Documents/notebook'
# 允许 root 用户运行
c.NotebookApp.allow_root = True
```

## 05.启动 juypter

```SHELL
jupyter notebook
```