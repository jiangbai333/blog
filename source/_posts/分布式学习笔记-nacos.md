---
title: 分布式学习笔记-nacos
date: '2022/08/18 11:22:53'
categories:
  - 分布式
  - nacos
tags:
  - nacos
  - 分布式
abbrlink: 5e3d8ee2
---
## 安装
访问[快速开始](https://nacos.io/zh-cn/docs/quick-start.html)，在版本选择中找到稳定版，下载。截止文章发布日，最新稳定版为[nacos 2.0.3](https://github.com/alibaba/nacos/releases/tag/2.0.3)
## 配置
下载完成后，会得到压缩包，解压为`nacos01`，进入`nacos01/conf`目录下，可以看到如下文件：

```shell
.
├── 1.4.0-ipv6_support-update.sql 
├── application.properties 默认的配置文件
├── application.properties.example 默认的配置文件示例
├── cluster.conf.example 集群配置示例
├── nacos-logback.xml 日志配置
├── nacos-mysql.sql mysql初始化脚本
└── schema.sql 嵌入式数据库(Derby)初始化脚本
```

<!-- more -->

`cluster.conf.example`、`application.properties.example`是两个示例文件，如果配置文件配置乱了，可以删掉，然后从这两个文件复制一份，把结尾`.example`去掉即可。

`nacos-mysql.sql`是nacos在使用mysql作为元数据存储时所用到的数据库表结构，本文以mysql作为nacos元数据的存储库，所以需要将这个sql文件导入到数据库中。

导入完成后，开始配置`application.properties`，作为基础应用，我们不需要额外的配置，只需要指定数据库连接就可以，在配置文件中添加如下配置：

```properties
spring.datasource.platform=mysql

db.num=1

db.url.0=jdbc:mysql://HOST:PORT/DATABASE?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user.0=用户名
db.password.0=密码
```

需要注意的是，在配置文件注释中，`db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?...`，其中`nacos`是对应的数据库名称，不能直接这样写，需要看我们本地创建的数据库的实际名称是什么，比如我们使用conf目录中`nacos-mysql.sql`脚本创建后，实际的名称为`nacos_config`。

配置完上述基础配置后，如果需要以集群模式启动，还需要配置`cluster.conf`，目录中默认不存在这个文件，按照上面说的，从`cluster.conf.example`复制一份：

```
cp cluster.conf.example cluster.conf
```

之后，在其中添加集群IP（这里写的是我本地的ip，你需要换成你的ip），理论上，集群有几个节点，就有几组ip:port，这里是在本机以三个不同端口启动的，需要注意的是，不要使用相连的端口：

```
192.168.1.75:8841
192.168.1.75:8843
192.168.1.75:8845
```

## 启动nacos服务
### 集群模式启动
配置完成后，由于我们需要以集群模式启动，所以需要把上面的`nacos01`再复制两份`nacos02`、`nacos03`，之后，分别进入其bin目录下运行startup.sh启动即可；

```
./nacos01/bin/startup.sh
./nacos02/bin/startup.sh
./nacos03/bin/startup.sh
```

查看进程，有三个运行中的nacos-server.jar，代表启动成功：

```
jps -l
72385 /nacos03/target/nacos-server.jar
72373 /nacos02/target/nacos-server.jar
72361 /nacos01/target/nacos-server.jar
```

### 单机模式启动
如果以单机模式启动，可以忽略本文中**配置nacos**章节，单机模式会使用内嵌数据库管理元数据，也不需要配置集群ip端口，直接指定单机参数启动即可：

```
./nacos01/bin/startup.sh -m standalone
```

## 访问nacos
通过[localhost:8841/nacos](http://localhost:8841/nacos)、[localhost:8843/nacos](http://localhost:8843/nacos)、[localhost:8845/nacos](http://localhost:8845/nacos)，均可以访问nacos配置页面，登录账号：nacos，密码：nacos。

## 停止nacos服务
想要停止服务，可以直接kill掉上面jps查出来的对应的进程。
