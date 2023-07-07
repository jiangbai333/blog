---
title: webpack入门系列教程-加载器
date: '2018/06/12 21:47:52'
categories:
  - javascript
  - webpack
tags:
  - javascript
  - nodejs
  - webpack
  - 构建&持续集成
abbrlink: 9c9b934e
---
# 简介
------
&emsp;&emsp;`loader`用于对模块的源代码进行转换，在import或加载模块时，对文件进行预处理。`loader`可将文件从不同的语言（如`TypeScript`、`sess`、或自定义语言）转换为`JavaScript`（前提是你得有相应的`loader`），亦可将内联图像转换为`data URL`。


# 常用`loader`
------
&emsp;&emsp;日常开发中，常用`loader`如下：
- `css-loader`：遍历css文件，处理样式
- `style-loader`：将样式插入到`<style></style>`标签中
- `sass-loader`: 便利文件，解析`sass`格式样式表
- `url-loader`: 根据需求将图片自动转成base64编码，减少网络负载
- `file-loade`：解决项目中，`url`资源的引入问题（ttf、woff、eot...等）
- `babel-loader`：添加ES6语法支持

<!-- more -->

# `loader`的使用
------
## 模块安装
&emsp;&emsp;首先，安装相应的`loader`:
```bash
# cd ~/project
# npm install file-loader css-loader style-loader sass-loader url-loader babel-loader --save-dev
```
&emsp;&emsp;接下来需要安装对应的解释器（`loader`可以理解成加载相应的模块，而解释器则是对加载后的模块进行翻译，生成最终的可执行代码）
```bash
# npm install babel-preset-env@next node-sass --save-dev
```
&emsp;&emsp;`babel-preset-env@next`与`node-sass`分别解释`babel-loader`与`sass-loader`的加载结果，那么你可能会问：“为什么其它的`loader`不需要安装解释器？”。原因是`babel-loader`与`sass-loader`的加载结果，并不是所有浏览器都能直接执行的，因此必须重新将它们翻译成通用的可执行代码。
&emsp;&emsp;这里需要注意的是，在安装`node-sass`的时候会安装不上，错误原因大概是其必须的二进制文件未下载成功，解决方法在这里贴出来，未能亲测（因为我之前安装成功过，直接把原来的拷贝过来了）。后续有时间，将会亲自测试这里。
```bash
# npm install node-sass --save-dev --registry=https://registry.npm.taobao.org --disturl=https://npm.taobao.org/dist --sass-binary-site=http://npm.taobao.org/mirrors/node-sass
```
** 本篇中暂不涉及到`babel-loader`与`babel-preset-env@next`的使用**

## 编写测试代码
&emsp;&emsp;首先我们补全测试用目录及文件：
```bash
projerct
├── app
│   ├── index.js
│   └── lib.js
│   └── es6.js
├── build
├── node_modules
│   ├── ...
├── package.json
├── package-lock.json
├── source
│   ├── image
│   ├── style
│   │   ├── css
│   │   │   └── index.css
│   │   └── sass
│   │       ├── index.scss
│   │       └── variables.scss
│   └── template
│       └── index.html
└── webpack.config.js
```
&emsp;&emsp;其中`source/image`、`source/style/css/`、`source/style/sass/`为新增目录，分别存放图片资源、css资源文件、sass资源文件。同时在`source/style/css/`目录中增加`index.css`文件、`source/style/sass/`目录中增加`variables.scss`、`index.scss`文件。同时在`app/`目录下增加`es6.js`文件。

&emsp;&emsp;修改我们位于`source/template/index.html`中的模板文件，分别建立针对css样式与sass样式的两个div容器：
```html
<html>
    <head>
        <meta charset="UTF-8">
        <title>My template app</title>
    </head>
    <body>
        <div class="css">
            <div><h1>css</h1></div>
            <div><h1>css</h1></div>
            <div><h1>css</h1></div>
        </div>
        <div class="sass">
            <div><h1>sass</h1></div>
            <div><h1>sass</h1></div>
            <div><h1>sass</h1></div>
        </div>
    </body>
</html>
```
&emsp;&emsp;编辑`source/style/css/index.css`，使拥有 .css 类名的div容器字体呈现红色：
```css
.css {
    color: red;
}
```
&emsp;&emsp;编辑`source/style/scss/variables.scss`，定义一个颜色变量：
```css
$color: blue;
```
&emsp;&emsp;编辑`source/style/scss/index.scss`，引入刚刚定义的变量，使拥有 .sass 类名的div容器字体呈现蓝色，并且为body添加一个背景图片：
```css
@import "./variables.scss";

.sass {
    color: $color
}

body {
    background: url('../../image/1.jpg');
}
```
&emsp;&emsp;编辑`app/es6.js`：
```javascript
export class Test {
    constructor(name) {
        this.name = name;
    }
}
```
&emsp;&emsp;编辑`app/index.js`，引入上述资源文件及模块：
```javascript
require('../source/style/css/index.css');
require('../source/style/sass/index.scss');

import {Test} from './es6.js';

let name = new Test("Baichao Jiang")

let element = document.createElement('h2');
element.innerHTML = "My name is " + name.name;
document.body.appendChild(element);
```
&emsp;&emsp;这里需要注意一点，`import`的模块名，必须与文件内所导出的类名一致。例如我们`app/es6.js`中写的是`export class Text`，那么在`import`时，后面必须跟的是`{Test}`才行。
&emsp;&emsp;在最后，编辑`webpack.config.js`，配置loader：
```javascript
var path = require('path');
var hwp = require('html-webpack-plugin')

var ROOT_PATH = path.resolve(__dirname);
var APP_PATH = path.resolve(ROOT_PATH, 'app');
var BUILD_PATH = path.resolve(ROOT_PATH, 'build');
var SOURCE_PATH = path.resolve(ROOT_PATH, 'source');

module.exports = {
    mode: 'development',
    entry: APP_PATH,
    output: {
        path: BUILD_PATH,
        filename: 'bundle.js'
    },
    module: {
        rules: [{
            test: /\.s?css$/,
            loaders: ['style-loader', 'css-loader', 'sass-loader'],
            include: ROOT_PATH
        }, {
            test: /\.(jpe?g|png|gif|ico)$/,
            loader: 'url-loader?limit=10240'
        }, {
            test: /\.((ttf|eot|woff2?|svg)(\?v=[0-9]\.[0-9]\.[0-9]))|(ttf|eot|woff2?|svg)$/,
            loader: 'url-loader?limit=10000&name=fonts/[hash:8].[name].[ext]'
        }]
    },
    plugins: [
        new hwp({
            title: 'hello world app',
            template:__dirname + '/source/template/index.html'
        })
    ],
    devServer: {
        historyApiFallback: true,
        hot: true,
        inline: true,
        progress: true,
    },
}
```
## 运行
&emsp;&emsp;在前面一节中，我们使用了`ebpack-dev-server`插件，因此直接运行就可以
```bash
# npm start
```
&emsp;&emsp;之后在浏览器产看页面即可，最终的页面效果如下：
![图片无法显示](/images/webpack入门系列教程-加载器/1.jpg)

# 遇到的几个错误
------
## 非程序错误

1. ** 标点符号别写错 **
2. ** 目录、文件名、类名等英文字母别打错 **


## webpack4 加载器关键字用`rules`
&emsp;&emsp;在某个版本之前，一直是用`loaders`做关键字的，后来应该是升级为`rules`了。在这里卡了半天。
## loaders里面要的写法
&emsp;&emsp;如果直接写成[`css`, `style`,...]会出现如下错误信息：
```bash
BREAKING CHANGE: It's no longer allowed to omit the '-loader' suffix when using loaders.
                 You need to specify 'style-loader' instead of 'style',
                 see https://webpack.js.org/guides/migrating/#automatic-loader-module-name-extension-removed
```
&emsp;&emsp;解决方法是写成`['*-loader',...]`，`*`代表所需的加载器模块名。该解决方案来自于[https://github.com/tcoopman/](https://github.com/tcoopman/image-webpack-loader/issues/59#issuecomment-277428044)

## 未完待续
