---
title: CentOS 6.8 从 Java 7 升级到 Java 8
description: 介绍了在 CentOS 6.8（AWS EC2） 上从 Java 7 升级到 Java 8 的方法。
categories: Linux
date: 2018-07-01 22:00:43
updated: 2018-07-01 22:00:43
tags:
---


CentOS 6.8 上默认安装了 JDK 1.7，当用 yum 安装 jdk 1.8 之后，通过配置环境变量，始终都无法将 Java 环境修改为1.8。

有两种方法解决：
1. 删除jdk 1.7
2. 使用 alternatives 配置

# 安装jdk 1.8

1. 搜索 openjdk
```shell
$ yum search openjdk

已加载插件：priorities, update-motd, upgrade-helper
1056 packages excluded due to repository priority protections
==================================================================== N/S matched: openjdk ====================================================================
java-1.6.0-openjdk.x86_64 : OpenJDK Runtime Environment
java-1.6.0-openjdk-demo.x86_64 : OpenJDK Demos
java-1.6.0-openjdk-devel.x86_64 : OpenJDK Development Environment
java-1.6.0-openjdk-javadoc.x86_64 : OpenJDK API Documentation
java-1.6.0-openjdk-src.x86_64 : OpenJDK Source Bundle
java-1.7.0-openjdk.x86_64 : OpenJDK Runtime Environment
java-1.7.0-openjdk-demo.x86_64 : OpenJDK Demos
java-1.7.0-openjdk-devel.x86_64 : OpenJDK Development Environment
java-1.7.0-openjdk-javadoc.noarch : OpenJDK API Documentation
java-1.7.0-openjdk-src.x86_64 : OpenJDK Source Bundle
java-1.8.0-openjdk.x86_64 : OpenJDK Runtime Environment
java-1.8.0-openjdk-demo.x86_64 : OpenJDK Demos
java-1.8.0-openjdk-devel.x86_64 : OpenJDK Development Environment
java-1.8.0-openjdk-headless.x86_64 : OpenJDK Runtime Environment
java-1.8.0-openjdk-javadoc.noarch : OpenJDK API Documentation
java-1.8.0-openjdk-javadoc-zip.noarch : OpenJDK API Documentation compressed in single archive
java-1.8.0-openjdk-src.x86_64 : OpenJDK Source Bundle
```

2. 安装jdk
```shell
$ sudo yum install java-1.8.0-openjdk.x86_64
```

3. 查看jdk版本
```shell
$ java -version

java version "1.7.0_171"
OpenJDK Runtime Environment (amzn-2.6.13.0.76.amzn1-x86_64 u171-b01)
OpenJDK 64-Bit Server VM (build 24.171-b01, mixed mode)
```

# 删除jdk 1.7

```shell
$ sudo yum install java-1.8.0
$ sudo yum remove java-1.7.0-openjdk
```

# 使用 alternatives 配置

```shell
$ sudo alternatives --config java

共有 2 个程序提供“java”。

  选择    命令
-----------------------------------------------
*+ 1           /usr/lib/jvm/jre-1.7.0-openjdk.x86_64/bin/java
   2           /usr/lib/jvm/jre-1.8.0-openjdk.x86_64/bin/java

按 Enter 来保存当前选择[+]，或键入选择号码：2
```