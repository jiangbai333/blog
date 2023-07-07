---
title: mysql开启远程访问(无废话)
tags:
  - mysql
  - 数据库
id: 509
categories:
  - 数据库
  - mysql
abbrlink: 4259446a
date: 2016-03-08 15:51:11
---

> 查看端口3306是否开启远程监听！

```bash
$ netstat -an | grep 3306 
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
```
##### 如果是上面这样`0 127.0.0.1:3306`或`0 :::3306`证明只在本机回路里监听，修改mysql配置文件`/etc/mysql/my.cnf`(如果你的配置文件不在这个目录，你可以用`find / -name "my.cnf"`命令查找出它的位置)
<!-- more -->

```bash
$ vi /etc/mysql/my.cnf

........
#将bind-address  = 127.0.0.1 前面加上 “#” 注释掉
#bind-address  = 127.0.0.1 
........

$ service mysql restart
```

##### 但是*即使你的 mysql 服务已经启动并成功运行，你也有可能遇到查不到3306端口的情况*！如下：
```bash
$ netstat -an | grep 3306 
$ lsof -i : 3306
$ ps aux|grep mysql
root      3713  0.1  0.2   5064  1368 pts/0    S    10:20   0:00 /bin/sh /opt/lampp/bin/mysqld_safe --datadir=/opt/lampp/var/mysql --pid-file=/opt/lampp/var/mysql/bogon.pid
mysql     4096  0.3 19.2 332004 98160 pts/0    Sl   10:20   0:00 /opt/lampp/sbin/mysqld --basedir=/opt/lampp --datadir=/opt/lampp/var/mysql --plugin-dir=/opt/lampp/lib/mysql/plugin/ --user=mysql --log-error=/opt/lampp/var/mysql/bogon.err --pid-file=/opt/lampp/var/mysql/bogon.pid --socket=/opt/lampp/var/mysql/mysql.sock --port=3306
root      4125  0.0  0.1   4356   736 pts/0    S+   10:22   0:00 grep mysql
```
##### 这是因为在 mysql 配置文件`my.cnf`中存在这样一段配置：
```bash
# Don't listen on a TCP/IP port at all. This can be a security enhancement,
# if all processes that need to connect to mysqld run on the same host.
# All interaction with mysqld must be made via Unix sockets or named pipes.
# Note that using this option without enabling named pipes on Windows
# (via the &quot;enable-named-pipe&quot; option) will render mysqld useless!
# skip-networking
```
##### 参数*skip-networking*起的作用是让 mysql 不再监听 TCP/IP 端口！此时，与mysqld的所有互动都必须通过Unix套接字或命名管道进行。需要将*skip-networking*注释掉，才能重新使 mysql 监听3306端口！

##### 将参数注释后，重新尝试第一步，你可以看到3306端口的监听状况！

> 接下来向防火墙添加3306规则

```bash
$ iptables -A INPUT -i eth0 -p tcp --dport 3306 -j ACCEPT
$ iptables -A OUTPUT -p tcp --sport 3306 -j ACCEPT
$ service iptables save
```

> 设置mysql用户的远程访问权限(假设开放 `test` 用户为远程访问用户)

```bash
$ mysql -u root -p
Enter password:

mysql> grant all privileges on *.* to `test`@"%" identified by "test用户的密码";
```
