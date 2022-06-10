---
title: IDEA 常用配置
date: 2018-07-07 22:00:00
updated: 2022-06-09 22:00:00
tags: ["idea"]
categories:
  - tool
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.配置 scalastyle

idea 本身已经支持 scalastyle，不需要下载插件，仅需要配置。首先，启用 scalastyle（默认已经开启了）：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-scalastyle.png)

然后，根据使用说明，我们需要将规则文件（注意命名方式）放置到 ***.idea*** 文件夹或者是***项目的根目录***。操作完成后，下面来验证配置是否生效，测试结果如下（*SpacesAfterPlusChecker* 规则要求加号后面必须有空格）：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/scalastyle-test.png)

## 02.解决控制台输出中文乱码

打开 `idea/bin/idea64.exe.vmoptions` ，添加 `-Dfile.encoding=UTF-8`，重启 IDEA

## 03.配置 checkstyle

首先，在 IDEA 插件市场中安装 CheckStyle-IDEA 插件，安装完成后，重启 IDE：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-install-checkstyle-plugin.png)

然后，按下图来配置 CheckStyle 插件：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-checkstyle-plugin-1.png)

配置完成后，启用插件：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-checkstyle-plugin-2.png)

下面来验证插件是否生效：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-checkstyle-plugin-3.png)

也可以使用右键菜单中的选项来手动触发 checkstyle：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-checkstyle-plugin-4.png)

>需要注意的是，上述过程只是拿 Flink 项目做演示，实际上，最好不要在 Flink 项目中做上面的配置。因为上述配置流程中的规则是对整个项目生效的，而 Flink 某些子模块需要特定的 supressions 文件，子模块的这些配置无法在 CheckStyle-IDEA 插件中体现，所以在 IDEA 中执行检查的时候会报错。对于 Flink 项目来说，我们可以执行 `mvn checkstyle:check` 来做代码检查，或者直接使用 IDEA 中的 Maven 插件，例如：
>
>![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/idea-config-checkstyle-plugin-5.png)

## 04.取消拼写检查

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/cancel_idea_spelling_check.png)

## 05.修改主题

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/set_idea_theme.png)

## 06.彻底删除项目

关闭 idea，删除文件系统中的项目，清空回收站。如果不清除回收站的话，再次创建同名项目会报错。

## 07.参考资料

- [各种办法解决IntelliJ IDEA控制台输出中文乱码问题](https://blog.csdn.net/qq_41431457/article/details/102680716)
- [Maven项目使用Checkstyle检查代码](https://blog.csdn.net/aouoy/article/details/106916263)
