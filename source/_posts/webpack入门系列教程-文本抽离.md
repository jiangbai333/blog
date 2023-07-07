---
title: webpack入门系列教程-文本抽离
date: '2018/06/27 21:28:00'
categories:
  - javascript
  - webpack
tags:
  - javascript
  - nodejs
  - webpack
  - 构建&持续集成
abbrlink: 34e0d73b
---
# 简介
------
&emsp;&emsp;如果你够细心的话，在前面几篇文章的例子里，你可能会发现我们所添加的 css 样式，全部写入到了所生成的 index.html 当中。而我们平时的开发中，往往是将样式独立出一个 `.css` 文件，然后引入到项目。
&emsp;&emsp;实际上，这两种做法，各自有各自的优点。
&emsp;&emsp;首先说写入到文件里的。要知道，web资源每次请求都是要消耗网络资源的，如果是通过 URL 引用样式文件，那么用户需要等待请求完成后才能看到渲染的界面，因此，在需要的样式较少时，实际上直接卸载`<style>`当中，是更好的选择，因为只需要访问页面时加载一次。
&emsp;&emsp;然而，当样式内容较多是时，单次相应已经不足以迅速完成，那么将样式表独立抽出较好，既方便调试、有不会产生请求的同步中断。
&emsp;&emsp;本篇文章将介绍如何在 `webpack` 打包时，抽离出独立的 css 文件，所用的项目为上一篇文章中建立的测试项目。

<!-- more -->

# 安装插件
------
&emsp;&emsp;上一篇文章中，我建议大家安装的`extract-text-webpack-plugin`便是今天的主角，如果你在上次练习中没有安装，那么现在进行安装：
```bash
# cs ~/project
# npm install extract-text-webpack-plugin@next --save-dev
```
# 修改配置
------
&emsp;&emsp;使用`extract-text-webpack-plugin`时需要修改`webpack`配置文件`webpack.config.js`，将上一篇文章中的`webpack.config.js`文件修改如下:
```javascript
const path = require('path');

const hwp = require('html-webpack-plugin')
/** 导入 extract-text-webpack-plugin 插件 */
const ExtractTextPlugin = require("extract-text-webpack-plugin");

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
        /** 将 css-loader、sass-loader 交由 extract-text-webpack-plugin 处理 */
        rules: [{
            test: /\.s?css$/,
            use: ExtractTextPlugin.extract({
                fallback: "style-loader",
                use: ["css-loader", "sass-loader"]
            })
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
        /** 指定抽离出的文件名，本例中为 styles.css */
        new ExtractTextPlugin("styles.css")
    ],
    devServer: {
        historyApiFallback: true,
        hot: true,
        inline: true,
        progress: true,
    },
}
```
&emsp;&emsp;可以看到，修改的项并不多。我们只需要将`extract-text-webpack-plugin`插件导入，将`css-loader`、`sass-loader`两个加载器指定给`extract-text-webpack-plugin`处理，并且添加一个`extract-text-webpack-plugin`导出实例就可以了。
&emsp;&emsp;配置完成后，所有样式将以`styles.css`通过`<link>`标签引入页面。

# 运行、对比结果
------
```bash
# cd ~/project
# npm start
```
&emsp;&emsp;访问页面后，按<F12>打开开发者工具：
### 文本抽离
![图片无法显示](/images/webpack入门系列教程-文本抽离/1.png)
&emsp;&emsp;可以看到通过`<link>`标签引入了`styles.css`
### 未进行文本抽离
![图片无法显示](/images/webpack入门系列教程-文本抽离/2.png)
&emsp;&emsp;全部样式都写到了页面的`<style>`标签中。


# 遇到的几个错误
------
## extract-text-webpack-plugin 版本问题
&emsp;&emsp;在前几次安装中一直按照下面方法安装的
```bash
# cs ~/project
# npm install extract-text-webpack-plugin --save-dev
```
&emsp;&emsp;但是安装完成后，一直报错，最后发现是由于`install extract-text-webpack-plugin`的版本过低，不支持`webpack4`。因此需要用以下方法安装：
```bash
# cs ~/project
# npm install extract-text-webpack-plugin@next --save-dev
```

## 一定要利用`extract-text-webpack-plugin`建立输出文件的实例
&emsp;&emsp;必须在 `plugins` 中增加 `new ExtractTextPlugin("filename.css")`
