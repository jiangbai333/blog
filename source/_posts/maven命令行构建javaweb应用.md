---
title: Maven命令行构建JavaWeb应用
date: '2016/11/02 14:04:00'
categories:
  - linux
  - maven
tags:
  - 软件
  - java
  - maven
abbrlink: 585d7e29
---
说起来，做java有段时间了，原先用eclipse开发的时候还好，转到sts简直卡出翔。早有抛弃IDE的打算，首要选择目标是VIM + MAVEN，这几天趁着有时间整理一下。废话到此为止，先说说maven那点儿事儿(以下均为linux环境下的操作与配置)！

>安装

首先下载最新版的MAVEN，在网上可以下载到 [二进制版](https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz "下载maven二进制版") 和 [源码版](https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.3.9/source/apache-maven-3.3.9-src.tar.gz "下载maven源码版") 。二进制版解压后配完环境变量就直接能用了，源码版需要编译。
<!-- more -->
```bash
# 下载 解压 更换目录将 maven 解压到 /opt 目录下
wegt https://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz
tar -zxvf ./apache-maven-3.3.9-bin.tar.gz -C /opt/

# 配置环境变量
export M2_HOME=/opt/apache-maven-3.3.9/
export M2=$M2_HOME/bin
export PATH=$M2:$PATH

# 查看 maven 版本 如能正常打印出 maven 版本号等信息 表示配置成功
mvn --version
```

>利用 MAVEN 构建 web 项目骨架

```bash
mvn archetype:generate -DgroupId=com.app -DartifactId=webAppName -DarchetypeArtifactId=maven-archetype-webapp -DinteractivMode=false -DarchetypeCatalog=internal
```
##### 各个参数的含义如下：
`DgroupId` : 指定包名
`DartifactId` : 指定构建的项目名称
`DarchetypeCatalog` : 指定 maven-archetype-plugin 读取 archetype-catalog.xml 文件的位置
- `internal` 读取 maven-archetype-plugin 内置的 .xml 文件
- `local` 读取本地 ~/.m2/archetype-catalog.xml 文件 
- `remote` 读取 Maven 中央仓库的 Catalog

`DarchetypeArtifactId` : 选择项目类型
- `maven-archetype-quickstart` 代表一个 Java 项目
- `maven-archetype-webapp` 代表一个 JavaWeb 项目

`DinteractiveMode` : 是否启用交互模式


##### 构建完成后，会在执行该命令的目录下生成一个 JavaWeb 项目骨架

>项目打包部署

##### 打包项目
```bash
mvn package
```
执行完此命令后，会在项目根目录生成 target 目录，将里面的`.war`拷贝到 tomcat 的 webapp 目录 启动服务器即可访问应用

##### 亦可利用 maven 代理部署
```bash
mvn package
mvn tomcat:run
```
