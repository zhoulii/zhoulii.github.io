---
title: IDEA 中导入 Calcite 源码
date: 2020-04-09 22:00:00
updated: 2022-06-09 22:00:00
tags: ["calcite"]
categories:
  - calcite
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.安装 Gradle

打开源码目录下的 `gradle/wrapper/gradle-wrapper.properties` 文件，确认 Gradle 的版本，下载并解压 Gradle 安装包，然后配置环境变量：

-   `GRADLE_HOME` ：Gradle 的解压目录
-   `GRADLE_USER_HOME` : Gradle 仓库目录（最好不要配置为 Maven 的本地仓库目录）
-   `PATH` : _$GRADLE_HOME/bin_

> 注意1：Gradle 的版本尽量与 Calcite 项目一致，否则有可能会报错
>
> 注意2：`GRADLE_USER_HOME` 最好不要配置为 Maven 的本地仓库目录，如果需要复用 Maven 的仓库，将 settings.xml 复制到 _$USER_HOME/.m2_ 即可，具体原因可查看参考资料。

## 02.配置 Gradle 仓库源

新建 `$GRADLE_HOME/init.d/init.gradle`，填写如下内容：

```KOTLIN
allprojects {
	repositories {
		mavenLocal()
		maven { url "https://maven.aliyun.com/repository/public" }
		maven { url 'https://maven.aliyun.com/repository/central'}
		maven { url 'https://maven.aliyun.com/repository/apache-snapshots'}
		maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
	}

	buildscript { 
		repositories {
			mavenLocal()
			maven { url "https://maven.aliyun.com/repository/public" }
			maven { url 'https://maven.aliyun.com/repository/central'}
			maven { url 'https://maven.aliyun.com/repository/apache-snapshots'}
			maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
		}
	}
}
```

## 03.配置项目插件仓库

方式1：在 `$GRADLE_HOME/init.d/init.gradle` 最外层添加如下内容

```KOTLIN
settingsEvaluated { settings ->
    settings.pluginManagement {
        repositories {
			maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
        }
    }
}
```

方式2：修改 Calcite 源码根目录下的 settings.gradle.kts，在 pluginManagement 层级下添加如下内容

```KOTLIN
repositories { 
    maven { 
        setUrl("https://maven.aliyun.com/repository/gradle-plugin") 
    }
}
```

## 04.导入源码到 IDEA

通过 `File->Open` 打开源码目录下的 build.gradle.kts 文件，在弹出的配置框中做如下配置（如果未弹出，则在 open 项目后去 Settings 中设置）：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/import-calcite-to-idea.png)

## 05.参考资料

-   [被误导的gradle使用本地maven仓库](https://blog.csdn.net/a386139471/article/details/107738615)
-   [Gradle 使用 Maven 的本地仓库(坑)](https://blog.csdn.net/kcp606/article/details/81636426)
-   [idea2020配置gradle并导入Spring源码](https://blog.csdn.net/weixin_41624318/article/details/108712319)
-   [Gradle 插件](https://www.cnblogs.com/skymxc/p/gradle-plugin.html)