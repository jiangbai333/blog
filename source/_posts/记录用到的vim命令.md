---
title: 记录用到的vim命令
date: '2016/12/16 09:10:00'
categories:
  - 软件
  - vim
tags:
  - vim
abbrlink: 373ee62a
---

本文将不定期的做出更新，记录用到以及接触过的vim命令。
<!-- more -->

# 移动
```bash
Nh 向左移动(N)列
Nj 向下移动(N)行
Nl 向右移动(N)列
Nk 向上移动(N)行

gg 移动到首行
G 移动到末行
NG 移动到第N行
N% 移动到百分之N

Nf[char] 移动到光标右边的第N个指定字符[char]上
Nt[char] 移动到光标右边的第N个指定字符[char]前
NF[char] 移动到光标左边的第N个指定字符[char]上
NT[char] 移动到光标左边的第N个指定字符[char]前

$ 移动到行尾
^ 移动到行首

w 移动光标到下一个单词的词首
b 移动光标到上一个单词的词首
e 移动光标到下一个单词的结尾
ge 移动光标到上一个单词的结尾

W 移动光标到下一个字符串的开始
B 移动光标到上一个字符串的词开始
E 移动光标到下一个字符串的结尾
gE 移动光标到上一个字符串的结尾

( 移动光标到上一个句子的开头
) 移动光标到下一个句子的开头
{ 移动光标到上一个段落的开头
} 移动光标到下一个段落的开头
```

# 复制粘贴
```bash
yw 复制当前光标处的一个单词
y0 复制当前光标处到行首(不含光标所在字符) 
y$ 复制当前光标处到行尾
yy 复制当前行
Nyy 从当前行开始向下复制n行

p 粘贴至光标后(下)
P(大写) 粘贴至光标前(上)
```

# 删除
```bash
x 删除当前光标处字符
dw 删除当前光标出一个单词
d0 删除光标处到行首的字符
d$ 删除光标处到行尾的字符
dd 删除当前光标所在行
Ndd 从当前光标所在行开始向下删除N行
df[char] 删除从光标位置到[char]字符(delete find [char])
d) 删除从光标位置到下一个句子的开始
d} 删除从光标位置到该段落的末尾
di{ 删除花括号之间的内容(delete inner {})(同"diB")
di( 删除小括号之间的内容(delete inner ())(同"dib")
dit 删除闭合标签之间的内容(html/xml等标签，delete inner tag)
dat 删除左右尖括号及之间的内容(delete a tag)
da< 删除左右尖括号及之间的内容(delete a <>)
di" 删除引号之间的内容(delete inner "")
da" 删除左右引号及之间的内容(delete a "")
:5,10d 删除5-10行
```

# 分屏及屏间操作
```bash
:sp file 垂直分屏并在新屏幕打开file文件
:vsp file 水平分屏并在新屏幕打开file文件

<c-w> w 切换至下一屏幕
<c-w> h 切换至右侧屏幕
<c-w> j 切换至下方屏幕
<c-w> k 切换至上方屏幕
<c-w> l 切换至左侧屏幕

<c-w> H 将当前屏幕与相邻右侧屏幕交换
<c-w> J 将当前屏幕与相邻下方屏幕交换
<c-w> K 将当前屏幕与相邻上方屏幕交换
<c-w> L 将当前屏幕与相邻左侧屏幕交换
```
