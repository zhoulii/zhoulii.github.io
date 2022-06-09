---
title: Maven 常用配置及操作
date: 2016-03-31 22:00:00
updated: 2022-06-09 22:00:00
tags: ["maven"]
categories:
  - tool

---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.Maven 设置阿里云镜像

修改 `$MAVEN_HOME/conf/settings.xml` 文件中的 mirrors 标签，配置如下：

```xml
<mirrors>
	<mirror>
		<id>nexus-aliyun</id>
		<name>Nexus aliyun</name>
		<url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		<mirrorOf>*</mirrorOf>        
	</mirror>
</mirrors>
```

## 02.Maven 配置本地仓库路径

找到 `$MAVEN_HOME/conf/settings.xml` 文件中的 `localRepository` 标签，将注释打开，并配置本地仓库路径，示例如下：

```xml
<localRepository>D:\repository</localRepository>
```

## 03.Maven 编译时跳过 test

跳过 test 的执行，但会编译：

```shell
mvn clean install -DskipTests
```

跳过 test 编译：

```shell
mvn clean install -Dmaven.test.skip=true
```

## 04.Maven 断点续编

```shell
mvn <goals> -rf :<moduleName>
```

## 05.Maven 离线安装依赖

在 shell 中执行如下命令来离线安装依赖包：

```shell
mvn install:install-file -DgroupId=io.confluent -DartifactId=kafka-schema-registry-client -Dversion=3.3.1 -Dpackaging=jar -Dfile=C:\Users\Leo\Downloads\kafka-schema-registry-client-3.3.1.jar
```

如果需要指定 classifier，可执行下面命令（需要注意 classifier 的位置，原因可参考《[Maven的classifier作用](https://www.cnblogs.com/lnlvinso/p/10111328.html)》）：

```shell
mvn install:install-file -DgroupId=org.apache.flink -DartifactId=flink-table-planner-blink_2.11 -Dversion=1.12-SNAPSHOT -Dclassifier=tests -Dpackaging=jar -Dfile=C:\Users\zhouli16\Downloads\flink-table-planner-blink_2.11-1.12-20200706.040013-54-tests.jar
```

> 问题：在 powershell 中执行会失败
>
> 解决：使用 CMD 来执行，或者给参数加上引号，如 `-DgroupId=io.confluent` 修改为 `-D"groupId=io.confluent"`

> 问题：通过离线方式安装依赖包时，maven-shade-plugin 无法将依赖的依赖一起打入 jar 包
>
> 解决：执行 `install:install-file` 时，使用 `-DpomFile=xxx` 来指定依赖的 pom 文件









