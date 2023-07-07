---
title: vim
abbrlink: 280100fb
date: 2016-07-02 21:47:11
tags:
---
利用 `Ctags` 在关键字中跳转
http://prdownloads.sourceforge.net/ctags/ctags-5.6.tar.gz下载、解压
`./configure && make && make install`
进入源码目录 `ctags -R` 
然后再vim中 `:set tags=源码目录/tags`
在函数处按 `<C-]>` 即可跳转到第一处 按 `<C-T>`跳回来源处
