---
title: svn开启提交前日志检查
date: '2018/02/22 16:55:00'
categories:
  - 软件
  - svn
tags:
  - svn
  - 软件
  - linux
  - 配置
abbrlink: 65cb392f
---
# 为什么这么做
------
&emsp;&emsp;SVN作为版本控制软件，主要用来管理项目、文件等的历史版本，并且给予一个支持多人协同开发的平台。当某一个版本出现错误或需求产生变化，需要切换至历史版本时，由于版本库里面，历史版本数量往往是比较庞大的，因此想要找到回溯点，就比较困难。另外，当交叉开发维护时，也会因为历史代码、逻辑不好寻找而耽误进度。
&emsp;&emsp;如果某项目参与人员，在提交过程中，没有给出提交日志，那么上述两个过程是十分让人痛苦的。好在我们有方法，强制提交者写明提交日志。下面进入正文！
<!-- more -->

# pre-commit
------
## 什么是pre-commit
&emsp;&emsp;`pre-commit`是SVN所提供的钩子脚本之一，它允许你利用`shall`、`python`等脚本语言，在SVN产生提交动作时，对提交内容进行认证。

## 编辑pre-commit，强制要求提交时写入日志
&emsp;&emsp;在此，我们假设你的SVN仓库建立在`/home/baichao/svn/`，仓库名为`test`
```bash
# cd /home/baichao/svn/test/hooks
# vi pre-commit
```
&emsp;&emsp;将下面内容写入到`pre-commit`文件中
```bash
#!/bin/sh

REPOS="$1"
TXN="$2"
comment=0

SVNLOOK=/usr/bin/svnlook
$SVNLOOK log -t "$TXN" "$REPOS" | grep "[a-zA-Z0-9\u4e00-\u9fa5]" || comment=1

if [ $comment -eq 1 ]
then
	echo "您的提交日志为空!
		输入本次提交的日志,以方便代码回溯" 1>&2
	exit 1
else
	exit 0
fi
```
&emsp;&emsp;保存后，赋予`pre-commit`文件可执行权限
```bash
# chmod +x pre-commit
```

&emsp;&emsp;无需重新启动SVN服务，即改即用！
