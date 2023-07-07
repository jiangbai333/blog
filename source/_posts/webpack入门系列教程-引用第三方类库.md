---
title: webpack入门系列教程-引用第三方类库
date: '2018/06/26 22:21:16'
categories:
  - javascript
  - webpack
tags:
  - javascript
  - nodejs
  - webpack
  - 构建&持续集成
abbrlink: 75e6ceda
---
# 简介
------
&emsp;&emsp;前文已经提到，`webpack`用来将种种模块打包，最终输出一个全量 js 文件，引入到页面。一些针对项目的功能性模块，固然是需要我们自己去完成，但是 node 所提供的丰富类库，更是我们开发时必不可少的工具。因此，我们在开发过程中，一定会涉及到结合第三方类库开发的情况。本节中，将通过实例，讲解如何在webpack中引用第三方类库。
<!-- more -->
# 整理项目结构
------
&emsp;&emsp;前文的练习中，我们构建了一套项目结构，用以测试相应的代码，下面我们重新建立一个项目，并修改项目结构。建立如下：
```bash
project
├── app
│   ├── index.js
│   ├── script.js
│   └── style.js
├── node_modules
│   └── ...
├── package.json
├── source
│   ├── image
│   │   └── 1.jpg
│   ├── libs
│   │   └── rander.js
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
&emsp;&emsp;简单介绍一下各个目录的约定规则：

1. `app/index.js`：项目入口文件
2. `app/script.js`：类库引用
3. `app/style.js`：样式引用
4. `node_modules`：通过 npm 安装类库
5. `source/libs`：存放我们自己写的类库
6. 其它目录及文件与原来的相同

# 安装依赖
------
## 插件安装
```bash
# npm install html-webpack-plugin extract-text-webpack-plugin@next uglifyjs-webpack-plugin --save-dev
# npm install webpack-dev-server -g
```
&emsp;&emsp;其中，我们本篇中暂不介绍 `extract-text-webpack-plugin@next`、`uglifyjs-webpack-plugin`的使用，用法将在后续文章介绍。

## 安装加载器及相关解释器
```bash
# npm install file-loader css-loader style-loader sass-loader url-loader babel-loader node-sass --save-dev
```
## 注意事项
&emsp;&emsp;以上所安装的依赖中，** 插件 **部分皆不是本文章所讲解功能的必须插件，但为了我们开发、调试便利，建议全部安装。
&emsp;&emsp;其中`html-webpack-plugin`、`webpack-dev-server`前面文章已经提到过，灵活使用它们，可以加快开发进度。
&emsp;&emsp;`extract-text-webpack-plugin`为文本抽离插件，将在后续文章举例讲解。
&emsp;&emsp;`uglifyjs-webpack-plugin`为压缩插件，将在后续文章举例讲解。
&emsp;&emsp;** 加载器 **部分，必须全部安装。

# 配置
------
&emsp;&emsp;根据前面所安装的依赖，对项目进行配置：
## package.json
```javascript
{
    ...
    "script": {
        ...,
        "start": "webpack-dev-server --hot --inline"
    }
    ...
}
```
&emsp;&emsp;** `...`代表源文件保持不变，因此上面的意思是：在`package.json`中找到`script`字段，并在其结尾添加`"start": "webpack-dev-server --hot --inline"`，并且在所添加的前一行结尾增加一个逗号`[,]`（参考js对象的写法，每个键值对后都要有个逗号），其它位置保持不变。
&emsp;&emsp;因为配置较多的话，重复内容过多，占用篇幅。因此，文章中其它配置，也会有这种写法，后文不在赘述 **。

## webpack.config.js
&emsp;&emsp;配置webpack运行时需要的配置文件：
```javascript
const path = require('path');
const hwp = require('html-webpack-plugin');

const ROOT_PATH = path.resolve(__dirname);
const APP_PATH = path.resolve(ROOT_PATH, 'app');
const BUILD_PATH = path.resolve(ROOT_PATH, 'build');
const SOURCE_PATH = path.resolve(ROOT_PATH, 'source');

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
        }),
    ],
    devServer: {
        historyApiFallback: true,
        hot: true,
        inline: true,
        progress: true,
    },
}
```
# 安装第三方类库
------
&emsp;&emsp;本篇文章中，我们使用`moment`库。
>A lightweight JavaScript date library for parsing, validating, manipulating, and formatting dates.
>引自 [Moment.js](https://github.com/moment/moment/ "Moment.js")

```bash
# npm install moment --save-dev
```
&emsp;&emsp;OK！等待控制台跑完，`Moment` 库就安装完成了，其实跟我们安装插件、加载器的方式是相同的。

# 编写与测试
------
## 编写代码
&emsp;&emsp;首先编辑`source/libs/rander.js`，我们在这个类中，引用了 Moment 库，并利用它向一个 DOM 元素渲染当前时间。
```javascript
import Moment from 'moment';

export class Rander {

    /** 构造方法 */
    constructor(dom) {
        this._dom = dom;
        this.randerTime();
    }

    /** 时间渲染方法 */
    randerTime() {
        this._dom.innerHTML = Moment().format('YYYY-MM-DD hh:mm:ss')
    }
}
```
&emsp;&emsp;很简单的一个工具类，在创建 Rander 实例时，传入一个 DOM 对象，然后利用 randerTime 方法想所传入的 DOM 对象，渲染当前时间。接下来是构建用于显示的 html 模板，以及相应样式文件。
### 模板文件
** `source/template/index.html`： **
```html
<html>
    <head>
        <meta charset='utf-8'>
        <title>Moment app</title>
    </head>
    <body>
        <div class="time"></div>
    </body>
</html>
```
### 样式文件
&emsp;&emsp;为了熟悉 `sass` ，我们同时使用 css样式表 与 sass样式表。

** `source/style/css/index.css`： **
```css
.time {
    color: #fff;
}
```
** `source/style/sass/variables.scss`： **
```css
$body-background-color: rgba(0, 0, 0, 0.72);

$time-margin: 20% auto;
$time-width: 500px;
$time-text-align: center;
```
** `source/style/sass/index.scss`： **
```css
@import './variables.scss';

body {
    background: $body-background-color;
}

.time {
    margin: $time-margin;
    width: $time-width;
    text-align: $time-text-align;
}
```

&emsp;&emsp;最后，编辑入口目录 `app` 下的脚本引入文件 `app/script.js`、`app/style.js` 引入我们上面写的的类库与样式文件，并将在入口文件 `app/index.js` 中导入上面两个文件。

** `app/script.js` **
&emsp;&emsp;在 `app/script.js` 中，我们将引入我们所写好的 `/source/libs/rander.js` 工具类，并且写入我们所需要的基本逻辑代码。如下代码所示，非常简单的一段代码，利用 Rander 对象，向 DOM 渲染当前时间：
```javascript
import {Rander} from '../source/libs/rander';

let rnd =  new Rander(document.querySelector(".time"));

(function loop() {
    rnd.randerTime();
    setTimeout(loop, 1000)
})()
```

** `app/style.js` **
&emsp;&emsp;在 `app/style.js` 中，引入所有样式文件：
```javascript
import '../source/style/css/index.css';
import '../source/style/sass/index.scss';
```

** `app/index.js` **
&emsp;&emsp;在 `app/index.js` 中，导入上述两个文件，完成本文全部代码：
```javascript
import './script.js';
import './style.js';
```


# 测试运行
------
&emsp;&emsp;如果你安装了 `webpack-dev-server` 插件，那么直接运行就可以：
```bash
# cd ~/project 
# npm start
```
&emsp;&emsp;如果你未安装 `webpack-dev-server` 插件，那么可以直接利用 `webpack` 命令打包，然后运行 `build/index.html` 文件即可：
```bash
# cd ~/project 
# webpack
```
&emsp;&emsp;之后在浏览器产看页面即可，最终的页面效果如下：
![图片无法显示](/images/webpack入门系列教程-引用第三方类库/1.gif)
