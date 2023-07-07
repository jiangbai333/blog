---
title: Github项目克隆提速
date: '2018/04/10 01:53:00'
categories:
  - 软件
  - git
tags:
  - git
  - 软件
abbrlink: 9c56abc4
---
# 简介
------
&emsp;&emsp;Github作为全球最大的同性交友网站，一直广受开发者们的喜爱，但是由于某些不可描述的原因，在访问、克隆、拉取、推送的时候，经常莫名其妙的不好使，速度慢的让人无法忍受。解决这个问题（克隆、拉取、推送）的一个途径就是通过代理，绕过** 某长城 **。

<!-- more -->

# 配置本地代理
------
&emsp;&emsp;关于代理服务器的事儿就不多说了，要么买，要么租vps自己搭建。一下内容的先决条件是你拥有一个好使的代理服务。
&emsp;&emsp;用`shadowsocks`开启代理后，右键点击托盘中的纸飞机，选择选项配置：

![图片无法显示](/images/Github项目克隆提速/git1.png)


&emsp;&emsp;设置一个本地代理端口号，如果需要局域网其它用户也通过此代理，需要勾选* 允许来自局域网的连接 *选项：

![图片无法显示](/images/Github项目克隆提速/git2.png)

# 设置git代理
------
&emsp;&emsp;通过以下命令设置git的代理：
```bash
git config --global http.proxy 'socks5://ip:1080' 
git config --global https.proxy 'socks5://ip:1080'
```
&emsp;&emsp;这里需要注意的是，如果本机使用的话，ip用`127.0.0.1`即可,局域网ip能不能用我没有测试。如果需要取消代理，可以用下面的代码：
```bash
git config --global --unset http.proxy 
git config --global --unset https.proxy 
```
