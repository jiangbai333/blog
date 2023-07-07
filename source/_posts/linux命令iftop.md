---
title: Linux命令iftop
date: '2017/03/06 14:21:00'
categories:
  - 软件
  - iftop
tags:
  - linux
  - 软件
  - 工具
abbrlink: 9ec20d25
---

> iftop is a command-line system monitor tool that produces a frequently updated list of network connections. [维基百科 iftop](https://en.wikipedia.org/wiki/Iftop "iftop-Wiki")


<!-- more -->

### 安装
-------
```bash
# 安装依赖
yum install libpcap libpcap-devel ncurses ncurses-devel
# 下载源码并解压编译
wget http://www.ex-parrot.com/pdw/iftop/download/iftop-0.17.tar.gz
tar -zxvf iftop-0.17.tar.gz
cd iftop-0.17.tar.gz
./configure
make
make install
```
-------
### 使用
-------

##### iftop
运行iftop。显示域名，这将会产生额外的流量

##### iftop -i eth0 -n
监测网卡eth0，显示IP。

-------
### 常用参数
-------
##### Host display:
- n - 切换DNS解析，显示IP或主机名 
- s - 切换显示/隐藏本机host信息
- d - 切换显/隐藏示远程主机host信息
- t - 一行显示模式（默认为两行）

##### Port display:
- N - 切换打开/关闭端口服务解析
- S - 切换显示/隐藏原主机端口
- D - 切换显示/隐藏远程主机端口
- p - 切换显示/隐藏端口信息

##### Sorting:
- 1/2/3 - 按1或2或3可以根据右侧显示的三列流量数据进行排序
- < - 按照原主机名排序 > - 按远端主机名排序
- o - 固定只显示当前的连接

##### General:
- P - 暂停/开始
- h - 切换显示help
- b - 切换显示流量条
- B - 切换计算2秒或10秒或40秒内的平均流量
- T - 切换是否显示每个连接的总流量
- j/k - 上下滚动显示
- L - 切换流量条刻度（linear scale and logarithmic scale）
