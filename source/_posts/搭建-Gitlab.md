---
title: GITLAB搭建(需要64位操作系统)
date: '2016/09/14 14:00:00'
categories:
  - linux
  - git
tags:
  - 软件
  - git
abbrlink: c981199c
---
首先说一下，我选用操作系统是`centos6.8 x86_64`，如果你安装到虚拟机的话，可能需要先开启CPU虚拟化才行，具体过程请自行[google](https://www.google.com/ncr "Title")

>首先安装依赖包并启动相应的服务

```bash
# 安装依赖
yum install curl postfix cronie openssh-server openssh-clients

# 启动并检查 postfix 邮件服务器
service postfix start
chkconfig postfix on

#安装 git
yum -y install git
```
<!-- more -->

>下载 Github 社区版

找一个镜像站(有 `Gitlab` 镜像的都行)，根据自己的需要选择不同的版本，老版本的安装更容易成功，我下载的是`gitlab-ce-8.0.4-ce.1.el6.x86_64.rpm`
```bash
#下载 rpm 安装包
wget https://mirror.tuna.tsinghua.edu.cn/gitlab-ce/yum/el6/gitlab-ce-8.0.4-ce.1.el6.x86_64.rpm

#更改安装包权限
chmod 777 gitlab-ce-8.0.4-ce.1.el6.x86_64.rpm

#安装
rpm -i gitlab-ce-8.0.4-ce.1.el6.x86_64.rpm
```

安装成功后，配置`/etc/gitlab/gitlab.rb`文件，找到`external_url`所在的位置并将它的值修改为你的`Gitlab服务器的域名或IP地址(局域网内，用本机ip即可 例如：external_url 'http://192.168.1.110')`，修改完成后，刷新配置信息、调整防火墙策略、启动`Gitlab`服务
```bash
#刷新配置
gitlab-ctl reconfigure

#调整防火墙策略放开 http、https、ssh 访问限制
lokkit -s http -s https -s ssh

#启动 gitlab 服务
gitlab-ctl start
```

在浏览器输入你`external_url`所配置的 ip 即可访问了！
### 默认的账户和密码是：
`Username: root`
`Password: 5iveL!fe`
如果你想注册账户，`Gitlab`会给你所填写的电子邮箱发送一封用于激活账号的e-mail，如果你在收件箱里找不到，那就到垃圾邮件里找找吧！！！

>GITLAB的汉化

想要给`Gitlab`安装中文语言包的话需要先克隆`Gitlab`源码
```bash
#停止 gitlab 服务
gitlab-ctl stop

#克隆 GitLab.com 仓库(速度慢但是肯定能用)
git clone https://gitlab.com/larryli/gitlab.git

#克隆 Gitcafe.com 镜像，速度更快(测试了一下，并不能顺利的克隆)
git clone https://gitcafe.com/larryli/gitlab.git
```
克隆完成后，接下来根据你所下载的`Gitlab`的版本制作汉化补丁并安装
```bash
#查看版本 我的是 8.0.4
cat /opt/gitlab/embedded/service/gitlab-rails/VERSION
8.0.4

#进入刚刚克隆好的Gitlab源码目录，比较此版本中文语言包分支与原分支的差异，并制作补丁文件 8.0 版本的汉化补丁（8-0-stable是英文稳定版，8-0-zh是中文版，两个 diff 结果便是汉化补丁）
cd gitlab
git diff origin/8-0-stable..origin/8-0-zh > /tmp/8.0.diff

#安装补丁
cd /opt/gitlab/embedded/service/gitlab-rails
git apply /tmp/8.0.diff  

#重新启动 gitlab
sudo gitlab-ctl start
```

### 可以看到中文界面啦！

>多说一句

由于克隆`Gitlab`源码比较耗时，如果你在虚拟机克隆成功了的话，建议你将克隆后的`gitlab`目录拷贝一下，因为当你包含 .git 目录一起拷贝时，你下次直接就能用
