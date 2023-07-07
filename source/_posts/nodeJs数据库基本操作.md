---
title: nodeJs数据库基本操作
date: '2016/10/14 10:23:00'
categories:
  - nodejs
tags:
  - 软件
  - nodejs
  - 数据库
abbrlink: fbfe7d5f
---

>本例以`mysql数据库`为例,在数据库操作之前需要通过`npm`安装`mysql`包

```bash
$ npm install mysql
```

>连接数据库

```javascript
var mysql=require('mysql');

var connection = mysql.createConnection({
    host     : '主机地址',
    user     : '用户名',
    password : '密码',
    database : '数据库名',
    port:'端口号'
});

connection.connect();
```
<!-- more -->

>插入数据

```javascript
var usr={name:'uname',password:'pword',mail:'jiangbai333@gmail.com'};
connection.query('insert into table set ?', usr, function(err, result) {
    if (err) {throw er;}

    console.log(result);
    console.log('\n');
});
```

>带条件更新数据

```javascript
connection.query('update table set password="ddd" where name="uname"', {password:'ppp'}, function(err, result) {
    if (err) {throw err;}

    console.log(result);
    console.log('\n');
});
```

>带条件删除数据

```javascript
connection.query('delete from table where name="uname"', {password:'ppp'}, function(err, result) {
    if (err) {throw err;}

    console.log(result);
    console.log('\n');
});
```

>查询数据

```javascript
connection.query('select * from table', function(err, rows, fields) {
    if (err) {throw err;}

    console.log('selected after deleted');
    for(var i= 0,usr;usr=rows[i++];){
        console.log('user name='+usr.name + ', password='+usr.password);
    }

    console.log('\n');

});
```
