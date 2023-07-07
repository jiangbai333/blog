---
title: Spring-HelloSpring
date: '2017/04/04 14:41:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
abbrlink: 652d9441
---
** 上一篇已经完成了spring开发环境的搭建，接下来，我们一起从一个简单的例子开始了解spring **

<!-- more -->

1.建立一个testController.java控制器
---------------------------
** 在`SpringMVC\src\main\java\com\spring\controller`路径下建立文件`testController.java`: **
```java
package com.spring.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 * 
 * @author hsoft
 */

/** 利用Controller注解，将testController类标记为一个控制器 */
@Controller
/** 利用RequestMapping注解，为testController类提供一个访问路径 */
@RequestMapping(value="/test")
public class testController {
	/** 利用RequestMapping注解，为index方法提供一个访问路径并指定请求模式 */
    @RequestMapping(value="/index", method = RequestMethod.GET)
    public String index(){
        System.out.println("控制器testController的index方法运行成功");
        return "index";
    }
}
```
2.建立一个index.jsp视图
---------------------
** 在`\SpringMVC\src\main\webapp\WEB-INF\page`路径下建立文件`index.jsp`: **
```html
<%@ page language="java" import="java.util.*" pageEncoding="UTF-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
  <head>
    <base href="<%=basePath%>">
    <title>Hello spring</title>
  </head>
  <body>
    <h1>Hello spring</h1>
  </body>
</html>
```
3.打包、运行项目
---------------------
在`\SpringMVC`目录下执行下列命令：
```bash
mvn clean
mvn package
mvn tomcat:run
```
** 控制台会打印tomcat启动信息，看到启动成功后，访问[http://localhost:8080/SpringMVC/test/index](http://localhost:8080/SpringMVC/test/index) **

4.spring都做了些啥
---------------------
** 首先说说`@Controller`、`@RequestMapping`这两个注解 **
** 回顾上一篇中`applicationContext-mvc.xml`文件中的一行配置： **
```bash
<context:component-scan base-package="com.spring"/>
```
** `context:component-scan`: 使得spring扫面我们的`com.spring`目录，恰巧我们的 `testController.java`在这个目录下，那么当我们使用`@Controller`注解时，spring将会把`testController`类标识为一个控制器并交由spring容器进行管理。虽然解析`@Controller`注解之后，`testController`已经交由spring容器进行管理了，但是实际上这时的spring并不能找到`testController`这个控制器。 **
** 真正让`testController`生效的是作用在类上的`RequestMapping`注解，`RequestMapping`注解提供了一个URL指向控制器的映射，在我们的例子中：/test -> testController **

** 举个比较容易理解的例子： **
A（客户端请求）要买房，B（控制器）要卖房。A并不认识B，B将自己的房子挂到网上写上了“卖”字（实际上“卖”字就相当于一个@Controller注解），这时C作为中介（spring容器）身份出现了，C看到B的房子上有个“卖”字，知道了B要卖房子，由于在发现B卖房子之前，还有很多人的卖房信息被C记录了，为了避免混乱，C单独用一个本子记录了B的信息（这里相当于`RequestMapping`注解），然后C就把B房子的地址、价格......都记录了下来（这相当于spring容器将控制器纳入管理）。
A向网络发布了房屋求购信息，C看到了，恰巧需求与B符合，OK，经C之手，将B的房子卖给了A！
也许有些不恰当的地方，但是大概就是这个意思！

** 接下来，说一下视图的渲染 **
** 回顾上一篇中applicationContext-mvc.xml文件中的四行配置：**
```bash
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/page/"/>
    <property name="suffix" value=".jsp"/>
</bean>
```
** 这里指定了 `InternalResourceViewResolver` 作为视图解析器。`prefix`、`suffix`标签分别配置了视图前缀和后缀为 `/WEB-INF/page/`、`.jsp`，当`testController`的`index`方法return时，return的内容“index”，被spring利用前缀和后缀合成为`/WEB-INF/page/index.jsp`,这样就渲染`/WEB-INF/page/`目录下`index.jsp`这张视图了 **
