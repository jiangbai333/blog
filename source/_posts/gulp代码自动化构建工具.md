---
title: gulp代码自动化构建工具
date: '2016/12/07 15:48:00'
categories:
  - nodejs
  - gulp
tags:
  - nodejs
abbrlink: 66edbe52
---

gulp 主要用来在部署代码前，对项目中的`.js、.html、.css`以及图片文件进行打包压缩，目的是减少在请求中的网络资源消耗。

# 安装
带`--save-dev`参数安装时，npm会自动下载相关依赖包，并且自动完成配置！
> 创建 gulp 工作目录`gulp_work`并安装 gulp 核心组件


<!-- more -->

```bash
# mkdir gulp_work
# cd gulp_work
# npm install gulp --save-dev 
```
> 安装 gulp 插件

```bash
# npm install gulp-minify-css gulp-concat gulp-uglify gulp-imagemin gulp-clean gulp-rename gulp-htmlmin --save-dev
```

|插件名			|用途			|
|---------------|---------------|
|gulp-imagemin	|图片压缩		|
|gulp-ruby-sass	|sass			|
|gulp-minify-css|css压缩			|
|gulp-jshint	|js检查			|
|gulp-uglify	|js压缩			|
|gulp-rename	|重命名			|
|gulp-concat	|合并文件		|
|gulp-clean		|清空文件夹		|
|gulp-htmlmin	|html压缩、处理	|

# gulp 工作目录结构

在 gulp_work 目录下，建立dist目录用来存放打包后的代码，建立src目录用来存放期望被打包处理的代码。在src目录下建立css、js、img、html目录，用来存放对应的`.css、.js、.html`与图片文件（注意！这种目录结构，仅仅是本文章例子中应用的，你完全可以自己定义一个符合你项目需求的目录结构，只要在构建文件里写好规则就行）

# 添加 gulp 入口文件 gulpfile.js
#### gulpfile.js
```javascript
var gulp = require('gulp'),
    imagemin = require('gulp-imagemin'), //图片压缩
    minifycss = require('gulp-minify-css'), //css压缩
    uglify = require('gulp-uglify'), //js压缩
    rename = require('gulp-rename'), //重命名
    concat = require('gulp-concat'), //合并文件
    clean = require('gulp-clean'), //清空文件夹
    htmlmin = require('gulp-htmlmin'); //html 处理
    
//var sass = require('gulp-ruby-sass'); //sass
//var jshint = require('gulp-jshint'); //js检查

//压缩css
gulp.task('minifycss',function() {
    var cssSrc = './src/css/*.css', //指向css源代码存放目录，如果你有自己的目录结构，可按需更改
        cssDst = './dist/css'; //指向css压缩代码输出目录，如果你有自己的目录结构，可按需更改
    return gulp.src(cssSrc) //为src方法提供被压缩文件
        .pipe(rename({ suffix: '.min' })) //输出文件后缀名添加 min
        .pipe(minifycss()) //执行压缩
        .pipe(gulp.dest(cssDst)); //输出文件夹
});

//压缩图片
gulp.task('imagemin',function(){
    var imgSrc = './src/images/*',
        imgDst = './dist/images';
    return gulp.src(imgSrc)
        .pipe(imagemin())
        .pipe(gulp.dest(imgDst));
});

//压缩js
gulp.task('uglify',function () {
    var jsSrc = './src/js/*.js',
        jsDst ='./dist/js';

    return gulp.src(jsSrc)
        /*.pipe(jshint('.jshintrc'))
        .pipe(jshint.reporter('default'))*/
        // .pipe(concat('main.js'))
        // .pipe(gulp.dest(jsDst))
        .pipe(rename({ suffix: '.min' }))
        .pipe(uglify())
        .pipe(gulp.dest(jsDst));
});

//压缩html
gulp.task('htmlmin', function () {
    var options = {
        removeComments: true, //清除注释
        collapseWhitespace: true, //压缩HTML
        collapseBooleanAttributes: true, //省略布尔属性的值 <input checked="true"/> ==> <input />
        removeEmptyAttributes: true, //删除所有空格作属性值 <input id="" /> ==> <input />
        removeScriptTypeAttributes: true, //删除<script>的type="text/javascript"
        removeStyleLinkTypeAttributes: true, //删除<style>和<link>的type="text/css"
        minifyJS: true, //压缩文件内JS
        minifyCSS: true //压缩文件内CSS
    };
    var htmlSrc = './src/*.html',
        htmlDst = './dist';
    gulp.src(htmlSrc)
        .pipe(htmlmin(options))
        .pipe(gulp.dest(htmlDst)); //此操作会替换输出目录下的同名html
});

//clean被执行时,先清空对应目录下图片、css、js
gulp.task('clean',function() {
    return gulp.src(['./dist/css', './dist/js', './dist/images'], {read: false})
        .pipe(clean());
});

//watch 监视 当文件发生更改，将自动处理
// gulp.watch('./src/css/*.css', ['minifycss']);
gulp.task('watch',function(){
    //css
    gulp.watch('./src/css/*.css', ['minifycss']);
    //css
    gulp.watch('./src/js/*.js', ['uglify']);
    //css
    gulp.watch('./src/images/*', ['imagemin']);
    //css
    gulp.watch('./src/*.html', ['htmlmin']);
});

//默认预设任务 清空图片、样式、js并重建 运行语句 gulp
gulp.task('default', ['clean'],function(){
    gulp.start('minifycss','uglify','imagemin','htmlmin','watch');
});
```

# 执行gulp
`src/js`目录下存在 test.js,内容为：
#### test.js
```javascript
(function() {
    var a = 1,
        b = 2;
    console.log(a + b);

    function a( ) {
        //asdasdasd
        var test;
    }
})()
```

执行gulp命令
```bash
# /opt/nodejs/bin/gulp 
[06:02:28] Using gulpfile /home/baichao/nodejs/gulp/gulpfile.js
[06:02:28] Starting 'clean'...
[06:02:28] Finished 'clean' after 22 ms
[06:02:28] Starting 'default'...
[06:02:28] Starting 'minifycss'...
[06:02:28] Starting 'uglify'...
[06:02:28] Starting 'imagemin'...
[06:02:28] Starting 'htmlmin'...
[06:02:28] Finished 'htmlmin' after 2.8 ms
[06:02:28] Starting 'watch'...
[06:02:28] Finished 'watch' after 15 ms
[06:02:28] Finished 'default' after 33 ms
[06:02:28] Finished 'minifycss' after 42 ms
[06:02:28] gulp-imagemin: Minified 0 images
[06:02:28] Finished 'imagemin' after 42 ms
[06:02:29] Finished 'uglify' after 98 ms
[06:05:05] Starting 'uglify'...
[06:05:05] Finished 'uglify' after 19 ms
```
你会在`dist/js`目录下看到一个test.min.js文件
#### test.min.js
```javascript
!function(){function n(){}var n=1,o=2;console.log(n+o)}();
```

你同时会注意到一点，终端内gulp实际上没有结束运行，此时如果你更改src内的文件，dist目录内将自动生成新版本的代码
