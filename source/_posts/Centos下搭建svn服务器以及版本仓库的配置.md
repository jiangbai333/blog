---
title: Centos下搭建svn服务器以及版本仓库的配置
tags:
  - linux
  - svn
id: 415
categories:
  - 软件
  - svn
abbrlink: '74053415'
date: 2015-08-25 23:26:16
---

# 安装 svn 服务端
```bash
# yum -y install subversion
# svn --version //察看svn版本
```
** 如果能正常察看到 svn 版本，证明 svn 安装成功，接着开始配置 svn **

# 创建并指定 svn 文件存放目录
```bash
# mkdir /opt/svndata //建立 svndata 主目录 
# svnadmin create /opt/svndata/test 在主目录下建立 test 仓库 
```

<!-- more -->

## 创建完成后 test 目录下生成 conf/ db/ hooks/ locks/ 目录以及 format 文件：
- conf 目录：存放此 svn 仓库-test仓库（下同）的配置文件
- db 目录：存放本仓库各个用户对文件修改与跟踪信息
- hooks 目录：钩子脚本目录，可以利用钩子脚本实现一些高级的 svn 操作
- locks 目录：？
- format 文件：存放当前版本库的唯一标识符

** 以上完成 svn 仓库的建立，接下来开始配置 svn 服务 **

# 配置 svn 仓库
## 配置 svnserve.conf 文件

** svnserve.conf 文件负责 svn 运行时的一些基本配置 **

```bash
# vi /opt/svndata/test/conf/svnserve.conf
[general]
#禁止匿名访问
anon-access = none
#允许写操作
auth-access = write   
#用户名密码键值对索引位置
password-db = passwd
#用户权限键值对索引位置
authz-db = authz
```

## 配置 passwd 文件

** passwd 文件负责存放登陆此 svn 资源库用到的用户名密码键值对，提供给 svnserve.conf 进行索引 **

```bash
# vi /opt/svndata/test/conf/passwd
[users]
test.user1 = passwd1
user2 = passwd2
user3 = passwd3
```

## 配置 authz 文件

```bash
# vi /opt/svndata/test/conf/authz
[groups]   
#将 test.user1、user2 用户添加到 admin 组，将 user3 添加到 users 组
admin = test.user1,user2   
users = user3
[/]
#admin 组赋予对多有目录的读写权限，user 组赋予对多有目录的读权限，其它用户赋予空权限，空权限将禁止其它用户访问
@admin = rw   
@user = r   
* =  
```
** 至此 svn 服务端配置完毕。启动 svn 服务并且开放 svn 服务默认端口,在客户端通过`svn://svn服务器ip地址/test`访问即可 **
```bash
# svnserve -d -r /opt/svndata/
# iptables -I INPUT -i eth0 -p tcp --dport 3690 -j ACCEPT
# iptables -I OUTPUT -o eth0 -p tcp --dport 3690 -j ACCEPT
# service iptables save 
```
