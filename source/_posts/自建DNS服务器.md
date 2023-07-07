---
title: 自建DNS服务器
date: '2016/08/24 11:51:00'
categories:
  - 软件
  - bind
tags:
  - DNS
  - 软件
  - linux
abbrlink: 7b7fe480
---
# 简介
------
&emsp;&emsp;它将域名和IP地址相互映射到一个分布式数据库，能够使人更方便地访问互联网。人们不需要去记住繁琐的ip地址，仅通过语义化的域名即可访问网络资源。
> [域名系统|DNS](https://zh.wikipedia.org/zh-cn/%E5%9F%9F%E5%90%8D%E7%B3%BB%E7%BB%9F "域名系统|DNS") - 引自维基百科
> [DNS服务器|域名服务器](https://baike.baidu.com/item/DNS%E6%9C%8D%E5%8A%A1%E5%99%A8 "DNS服务器|域名服务器") - 引自百度百科
<!-- more -->

# 安装
------
## BIND是什么
&emsp;&emsp;BIND是一个应用程序，它允许你构建一个本地的DNS映射服务。它应用广泛，全球90%以上的DNS服务器都是由它驱动的。
> 伯克利互联网名称服务 (Berkeley Internet Name Daemon，简称 [BIND](https://www.isc.org/downloads/bind/ "BIND主页")) 是 DNS 协议的一个参考实现
> 引自[ArchWiki](https://wiki.archlinux.org/index.php/BIND "ArchWiki")

## 基于yum安装BIND
```bash
# yum -y install bind*
```

# 配置
------
&emsp;&emsp;假定你要在局域网中映射你的项目地址（192.168.1.100）到域名`www.baichao.com`，并且你的DNS服务器所分配到的内网ip为（192.168.1.200）。
## 编辑named.conf文件
** `named.conf`文件是BIND的主配置文件，位于`/etc`目录下，备份后打开：**
```bash
# cp /etc/named.conf /etc/named.conf.bak
# vim /etc/named.conf
```
** 编辑为以下格式: **
```bahs
options {
    //bind监听53端口。监听任何主机
    listen-on port 53 { any; };
 
    //指定区域文件所放置的目录
    directory "/var/named";

    //类似于日志信息，指定该信息的保存位置
    dump-file       "/var/named/data/cache_dump.db";
    statistics-file "/var/named/data/named_stats.txt";
    memstatistics-file "/var/named/data/named_mem_stats.txt";
	
    //允许任何人发起DNS查询请求
    allow-query { any; };

    //允许递归查询
    recursion yes;

    //开启主从模式后，主服务器区域文件发生改变时，通知从服务器更新
    notify yes;

    //DNS安全加强
    dnssec-enable yes;
    dnssec-validation yes;
    dnssec-lookaside auto;
	
    //上层DNS服务器地址
    forwarders {
       8.8.8.8;
    };

    bindkeys-file "/etc/named.iscdlv.key";
    managed-keys-directory "/var/named/dynamic";
};

//运行日志
logging {
    
    //通道(channel)配置
    channel default_debug {

        //日志文件存放位置 并设置版本与单个日志文件大小
        file "data/named.run" versions 5 size 20m;
        
        //指定消息级别
        severity dynamic;
        print-time yes;
        print-category yes;
    };
};

//根域区域文件属性配置
zone "." IN {
	
    //设置区域文件类型
    type hint; 

    //根域区域文件
    file "named.ca";
};

//为方便配置管理，将配置分类存放再由主配置文件引入
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

```
### 部分配置说明
- 区域文件类型 zone type
  - hint 根域
  - master 主域
  - slave 从域
  - forward 转发域
- {}内部必须包含空格，{string} 错误， { string } 正确
- logging
  - 通道
    - 消息级别
      - critical
      - error
      - warning
      - notice
      - info
      - debug [ level ]
      - dynamic

## 编辑named.rfc1912.zones文件
** `named.rfc1912.zones`文件用于域配置，位于`/etc`目录下，备份后打开：**
```bash
# cp /etc/named.rfc1912.zones /etc/named.rfc1912.zones.bak
# vim /etc/named.rfc1912.zones
```
** 在文件结尾添加以下内容 **
```base
//定义要解析主域名（类似于根域配置）
zone "baichao.com" IN {
    type master;

    //区域文件 将保存在 /var/named 目录下
    file "baichao.com.zone"；
};
```
## 建立区域文件baichao.com.zone
** 此区域文件的文件名对应`named.rfc1912.zones`文件中你要解析的域名，将此文件建立在`/var/named`目录下，建立文件`baichao.com.zone`: **
```base
# vim /var/named/baichao.com.zone
```
** 将以下内容写入: **
```base
$TTL 86400
@       IN      SOA     ns.baichao.com. root (
                                            1       ; serial 区域文件版本号，用于主从同步
                                            1D      ; refresh 从服务器区域文件记录刷新时间
                                            1H      ; retry 从服务器更新失败后，下次尝试更新时间
                                            1W      ; expire 从服务器更新失败后，原有数据的过期时间
                                            0 )     ; minimum  被缓存的否定回答的存活时间

@       IN      NS      ns.baichao.com.
ns      IN      A       192.168.1.200
www     IN      A       192.168.1.100
```

# 添加防火墙规则
------

> 但是这里需要跟大家报告的是，通常 DNS 查询的时候，是以 udp 这个较快速的数据传输协议来查询的， 但是万一没有办法查询到完整的信息时，就会再次的以 tcp 这个协定来重新查询的！所以启动 DNS 的 daemon (就是 named 啦) 时，会同时启动 tcp 及 udp 的 port 53 喔！所以，记得防火墙也要同时放行 tcp, udp port 53 呢！ 
>
>引自[鸟哥的linux私房菜](http://cn.linux.vbird.org/linux_server/0350dns.php#DNS_master_start "鸟哥的linux私房菜")

** 开放`53`、`953`端口: **
```base
# iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 53 -j ACCEPT
# iptables -A INPUT -p udp -m state --state NEW -m udp --dport 53 -j ACCEPT
# iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 953 -j ACCEPT
```

** 被映射到的服务地址也需要开放服务端口: **
```base
iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
```

# 启动与扩展
------
## 启动
** 启动服务: **
```bash
# service named restart
Stopping named:                                            [  OK  ]
Starting named:                                            [  OK  ]
```
** 有时候会遇到启动失败的情况: **
```bash
# service named restart
Stopping named:                                            [  OK  ]
Starting named:                                            [FAILED]
```
** 此时并不会报出任何错误，你需要查看`/var/log/message`文件，查找出详细的错误信息并纠正: **
```bash
cat /var/log/message | grep named
```
## 扩展
&emsp;&emsp;这里是题外话，想要科学上网的朋友，可以自建一个DNS服务器，映射到目标地址。
