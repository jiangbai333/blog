---
title: webpack入门系列教程-安装及建立项目
date: '2018/04/04 09:02:00'
categories:
  - javascript
  - webpack
tags:
  - javascript
  - nodejs
  - webpack
  - 构建&持续集成
abbrlink: c74b4c00
---
# 简介
------
&emsp;&emsp;本篇中不介绍概念性的描述，仅给出可以顺利运行的操作方法与相应的代码、配置。关于本文中用到的软件、类库的概念性的介绍，我将在另外几篇文章中，做详细解释。

# 开发环境安装
------
## 安装nvm
&emsp;&emsp;本系列文章利用`nvm`对`node`进行版本管理
### wget
```bash
# wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```
<!-- more -->
### curl
```bash
# curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
```
### `nvm: command not found`的解决方法
```bash
# source ~/.bashrc 
```
### 安装最新稳定版本`node`
```bash
# nvm install stable
```
** 如果下载速度慢，建议将`nvm`数据源切换成taobao的 **
```bash
# NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
# nvm install stable
```
### 查看当前的node版本
```bash
nvm current
v9.10.1
```

## 安装`webpack`其相关依赖
```bash
# npm install webpack-cli -g
# npm link webpack
```

** 如果安装速度较慢，建议将`npm`数据源切换成taobao的 **
```bash
# npm config set registry http://registry.npm.taobao.org
```

## 建立`webpack`“项目”
&emsp;&emsp;严格来说，这并不能叫做一个项目，因为`webpack`仅仅是一个构建工具集合，我们做的只是用它来对我们的现有项目进行构建而已。

### 初始化并完善目录结构
```bash
# mkdir project
# cd project
# npm init
```
&emsp;&emsp;执行`npm init`后，将出现对话流程，按照提示，输入你想生成的信息。执行完成后，将会在`project`目录下，生成用于运行的`package.json`文件，文件内是项目的描述信息（你刚刚输入的信息）。
&emsp;&emsp;接下来将目录及文件结构补全为下面的形式：
- app
    - index.js
    - lib.js
- package.json
- webpack.config.js

&emsp;&emsp;其中`app`目录为你的源码目录，其中`index.js`文件是入口文件，`lib.js`是你的模块文件。`webpack.config.js`是webpack的配置文件。

### 编辑文件
&emsp;&emsp;编辑目录下的各个文件，用来测试`webpack`打包。

** 模块文件`lib.js` **:
```javascript
function test() {
    return "Hello world";
}

/** 导出这个对象 */
module.exports = test;
```

** 入口文件`index.js` **:
```javascript
var myLib = require('./lib');
var dom  = document.querySelector("body");

dom.innerHTML = '<h1>' + myLib() + '</h1>';
```

** webpack配置文件`webpack.config.js` **
```javascript
var path = require('path');
var hwp = require('html-webpack-plugin')

var ROOT_PATH = path.resolve(__dirname);
var APP_PATH = path.resolve(ROOT_PATH, 'app');
var BUILD_PATH = path.resolve(ROOT_PATH, 'build');

module.exports = {
    mode: 'development',
    entry: APP_PATH,
    output: {
        path: BUILD_PATH,
        filename: 'bundle.js'
    },

    plugins: [
        new hwp({
            title: 'hello world app'
        })
    ]
}
```

### 安装相关依赖
&emsp;&emsp;进入主目录`~/project`
```bash
# cd ~/project
# npm install html-webpack-plugin --save-dev
```

# webpack打包
----
&emsp;&emsp;运行打包命令时，需要在`project`目录下
```bash
# cd /project
# webpack
Hash: 4d0cdffd153633cd3a5a
Version: webpack 4.4.1
Time: 670ms
Built at: 2018-4-25 21:23:06
     Asset       Size  Chunks             Chunk Names
 bundle.js   3.27 KiB    main  [emitted]  main
index.html  186 bytes          [emitted]
Entrypoint main = bundle.js
[./app/index.js] 115 bytes {main} [built]
[./app/lib.js] 67 bytes {main} [built]
Child html-webpack-plugin for "index.html":
     1 asset
    Entrypoint undefined = index.html
       4 modules
```

&emsp;&emsp;打包完成后，将会自动建立`build`目录，其中为打包好的`bundle.js`,以及由`html-webpack-plugin`插件自动为我们生成的`index.html`。在`index.html`中自动引用的打包好的`bundle.js`，可以直接用浏览器打开查看效果。







