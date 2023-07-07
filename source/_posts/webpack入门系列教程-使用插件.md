---
title: webpack入门系列教程-使用插件
date: '2018/04/22 21:47:52'
categories:
  - javascript
  - webpack
tags:
  - javascript
  - nodejs
  - webpack
  - 构建&持续集成
abbrlink: 85b936fe
---
# 简介
------
&emsp;&emsp;前一篇中，我们介绍了`webpack`的安装，并且通过一个简单的例子让其帮我们打包一个项目，可以看到，经过`webpack`打包之后，使得部署版的代码文件尺寸，更加的小，从而减小在带宽消耗。但是结合各种构建插件后的`webpack`，强大之处远不只如此...！
&emsp;&emsp;本章节中，将介绍开发中几个常用的插件的功能与基本用法，为后续文章中的示例内容打下基础。各个插件的详细用法，我将会另开一个专题进行详细讲解。
<!-- more -->
# 常用插件的安装与使用
------
## `html-webpack-plugin`
### 功能
&emsp;&emsp;在前一节中，我们使用到了这个插件，它帮我们在打包之后，自动生成一个引用打包后的js脚本的html文件。
### 安装
&emsp;&emsp;进入项目主目录，执行：
```bash
# npm install html-webpack-plugin --save-dev
```
&emsp;&emsp;执行后，`package.json`中将自动引入此依赖。
### 使用
&emsp;&emsp;在`webpack.config.js`文件中的引入`html-webpack-plugin`模块，并且在`plugins`属性内，增加如下配置：
```javascript
...
var hwp = require('html-webpack-plugin')
...
module.exports = {
    ...
    plugins: [
        new hwp({
            title: 'hello world app'
        })
    ]
    ...
}
```
&emsp;&emsp;其中，`hwp`方法参数中，代表生成的html的title为`hello world app`。完成上述步骤之后，在进入项目根目录进行打包：
```bash
# cd ~/project
# webpack
```
&emsp;&emsp;打包完成后，可在`output`指向的目录中生成相应的文件。
&emsp;&emsp;因为在开发阶段，不可能每次都利用默认生成的`html`文件来重新改写，因此在使用`html-webpack-plugin`时，亦可指定一个模板文件，首先建立要存放模板文件的目录与相应的模板文件：
```bash
# cd ~/project
# mkdir -p source/template/
# touch source/template/index.html
```
&emsp;&emsp;建立完成后，编辑此`html`文件：
```html
<html>
    <head>
        <meta charset="UTF-8">
        <title>My template app</title>
    </head>
    <body>
        <div><h1>item 2</h1></div>
        <div><h1>item 1</h1></div>
        <div><h1>item 3</h1></div>
    </body>
</html>
```
&emsp;&emsp;修改`webpack.config.js`，使`html-webpack-plugin`引用此文件：
```javascript
...
module.exports = {
    ...
    plugins: [
        new hwp({
            template:__dirname + '/source/template/index.html'
        })
    ]
    ...
}
```
&emsp;&emsp;之后，在进入项目根目录进行打包：
```bash
# cd ~/project
# webpack
```
&emsp;&emsp;查看`output`目录中生成的`index.html`文件，会发现，其为模板文件附加一条打包好的js的引用。
## `webpack-dev-server`
### 功能
&emsp;&emsp;此插件相当于一个热部署插件，使用后无需在不停的进行打包-查看-修改-打包-查看...，修改完，可直接执行打包部署，同时会内建一个web服务，你可通过浏览器，查看打包完成后的效果，并且在发生更改是，将自动推送至浏览器。
### 安装
&emsp;&emsp;此插件不属于项目依赖，因此直接进行全局安装比较方便，安装完成后，后续的其它项目可直接配置并使用。
```bash
# npm install webpack-dev-server -g
```
### 使用
&emsp;&emsp;在`package.json`文件中增加执行脚本映射，当执行`npm start`时将自动打包部署：
```javascript
{
    ...
    "scripts": {
        ...
        "start": "webpack-dev-server --hot --inline"
        ...
    }
    ...
}
```
&emsp;&emsp;编辑完成后，进入项目主目录：
```bash
# cd ~/project
# npm start
```
&emsp;&emsp;等待启动完成后，可在浏览器通过`http://ip:8080`访问生成的页面。再次修改任何项目资源文件，浏览器均可自动刷新为最新的状态。

## extract-text-webpack-plugin
&emsp;&emsp;由于此插件需要与加载器结合使用，因此将放到后面做一篇特别讲解。

## uglifyjs-webpack-plugin
** 未完待续 **










