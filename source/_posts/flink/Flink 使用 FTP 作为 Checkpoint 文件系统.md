---
title: Flink 使用 FTP 作为 Checkpoint 文件系统
date: 2020-06-29 22:00:00
updated: 2022-06-08 22:00:00
tags: ["Flink"]
categories:
  - Flink
---

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/panda-banner-1.png)

<!-- more -->

## 01.背景说明

`Flink Job` 在运行失败时，可以从上一个 `checkpoint` 进行恢复，不过这有一个前提，就是 `checkpoint` 数据对所有的 `Task` 来说都是可以访问的。通常，我们会将 `checkpoint` 目录设置为 `HDFS`，但在一些轻量级的生产场景中是没有部署 `HDFS` 的，所以考虑使用 `FTP` 来保存 `checkpoint` 数据。

## 02.实现思路

`Flink` 可以使用 `HDFS` 作为 `checkpoint` 目录的原因是 `Flink` 支持 `HDFS` 文件系统，所以如果 `Flink` 可以支持 `FTP` 文件系统，那么我们就可以用 `FTP` 来存储 `checkpoint` 数据。查看 `hadoop` 的 `hadoop-common` 模块，可以发现 `FTP` 对应的文件系统实现是 `FTPFileSystem`，而 `Flink` 文件系统又是兼容 `Hadoop` 文件系统的，所以只要引入 `hadoop-common` 模块便可以让 `Flink` 支持 `FTP`，不过，为防止类冲突，最好引入 `Flink` 提供的 `flink-shaded-hadoop`。

## 03.实现步骤

-   将 `flink-shaded-hadoop-2-uber-xxx.jar` 放到 `Flink` 的 `lib` 路径下（必须放到 `lib` 路径下，打在作业 `Jar` 中加载不到）；
-   重启 `Flink Standalone` 集群;
-   作业代码中将 checkpoint 目录设置为 ftp，设置方式如下：
```java
env.setStateBackend(new RocksDBStateBackend("ftp://user:password@ip:port/path").asInstanceOf[StateBackend])
```

## 04.问题分析

提交作业到集群，可以发现 `Job` 是正常运行的，不过查看 `JobManager` 的日志可以发现，checkpoint 出错了，错误信息如下：

![](https://raw.githubusercontent.com/zhoulii/figure-bed/main/fig/ftp-checkpoint-error.png)

这里给出的错误原因是无法创建 `checkpoint` 目录，而此时 `FTP` 服务是正常的，手动创建目录、上传文件都没问题，所以可以排除 `FTP` 本身的问题。经过代码调试发现，问题是由路径引起的，因为使用 `FTPFileSystem` 创建目录时，路径必须是 `/A/B/C` 这样的形式，像 `ftp://user:password@ip:port/path` 这样的路径是不被 `FTPFileSystem` 支持的。

## 05.自定义 FTPFileSystem

自定义`FTPFileSystem` 来支持 `ftp://user:password@ip:port/path`  格式的路径，实现步骤如下：

**1.创建一个 Maven 项目，pom 写法如下：**

```xml
<!-- 依赖 -->
<dependencies>
	<dependency>
		<groupId>org.apache.flink</groupId>
		<artifactId>flink-shaded-hadoop-2-uber</artifactId>
		<version>2.7.5-10.0</version>
	</dependency>
</dependencies>
<!-- 打包插件 -->
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-shade-plugin</artifactId>
			<version>3.2.4</version>
			<executions>
				<execution>
					<phase>package</phase>
					<goals>
						<goal>shade</goal>
					</goals>
					<configuration>
						<artifactSet>
							<excludes>
								<exclude>com.google.code.findbugs:jsr305</exclude>
								<exclude>org.slf4j:*</exclude>
								<exclude>log4j:*</exclude>
							</excludes>
						</artifactSet>
						<filters>
							<filter>
							<!-- Do not copy the signatures in the META-INF folder.
							Otherwise, this might cause SecurityExceptions when using the JAR. -->
							<artifact>*:*</artifact>
							<excludes>
								<exclude>META-INF/*.SF</exclude>
								<exclude>META-INF/*.DSA</exclude>
								<exclude>META-INF/*.RSA</exclude>
							</excludes>
							</filter>
						</filters>
					</configuration>
				</execution>
			</executions>
		</plugin>
	</plugins>
</build>
```

**2.复制 `FTPFileSystem` 到项目中，并将其重命名为 `HFTPFileSystem`**

**3.修改 `HFTPFileSystem` 中的 `makeabsolute` 方法，修改后的方法如下：**

```java
private Path makeAbsolute(Path workDir, Path path) {
	Path absolute;
	if("hftp".equals(path.toUri().getScheme())){
		final String pathStr = path.toUri().getPath().substring(1);
		absolute = new Path(workDir, pathStr);
	} else {
		if (path.isAbsolute()) {
			return path;
		}
		absolute = new Path(workDir, path);
	}
	return absolute;
}
```

**4.修改 `HFTPFileSystem` 中的 `getSchema` 方法，修改后的方法如下：**

```java
public String getScheme() {
	return "hftp";
}
```

**5.在 `resources/META-INF/services` 目录下新建一个名为 `org.apache.hadoop.fs.FileSystem` 的文件，内容为 `HFTPFileSystem` 的全路径名**

**6.将项目打成 `jar` 包**

## 06.自定义 FTPFileSystem 的使用

-   将开发好的项目 `jar` 包放到 `Flink` 的 `lib` 路径下（必须放到 `lib` 路径下，打在作业 `jar` 中加载不到）；
-   重启 `Flink Standalone` 集群；
-   作业代码中将 `checkpoint` 目录设置为 `ftp`，协议头使用 `hftp`，设置方式如下：

```java
env.setStateBackend(new RocksDBStateBackend("hftp://user:password@ip:port/path").asInstanceOf[StateBackend])
```

此时，提交作业到集群，可以发现 `Job` 是正常运行的，`checkpoint` 数据也顺利落地到所配置的 `FTP` 目录。

## 07.参考资料

-   [Flink的standalone运行模式之State存储----不使用HDFS](https://blog.csdn.net/weixin_41917987/article/details/90294765)