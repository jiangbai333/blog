---
title: Spring-添加业务逻辑
date: '2017/04/07 09:24:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
abbrlink: a5d4ed7c
---
** 本节将在前面章节的基础上添加service层 **
1.准备工作
---------------------------

<!-- more -->

** 修改pom.xml，添加json-lib依赖 **
```bash
<!--json-lib-->    
<dependency>    
    <groupId>net.sf.json-lib</groupId>    
    <artifactId>json-lib</artifactId>    
    <version>2.4</version>    
    <classifier>jdk15</classifier>    
</dependency>
```

** 了解`@Autowired`、`@Service`注解 **

`@Service`：用来声明一个service服务类，被注解标注后由spring容器接管控制权；
`@Autowired`：用来进行依赖注入，从spring容器中获取实体对象的控制权；

2.编写service层代码
---------------------------
** 首先说一点，大部分开发过程中，每个模块的service层都会先定义一个接口，然后再写一个实现类。实际上意义不大，一方面来讲出于复用性，每个模块单独声明一个接口没有意义！另一方面，接口在团队开发时能提供一个对象结构的优先知悉性，眼下很多模块都是个人独立完成，所以声明接口也没有什么实际意义。废话说完进入主题(OK!我只是不想写接口DEMO V_V); **
** 在`\src\main\java\com\spring\service`目录下建立`test`包，并在`test`包下建立`testService.java`文件 **
```bash
package com.spring.service.test;

import net.sf.json.JSONObject;
import org.springframework.stereotype.Service;

@Service
public class testService {
    public String getTestData(String key) {
        JSONObject jo = new JSONObject();
        jo.accumulate(key, "service返回的数据");
        return jo.toString();
    }
}
```

** service层很简单，仅仅利用controller层传来的参数key构建一个{key:"service返回的数据"}的json对象 **
3.修改controller代码，访问service层
----------------------------------
** 我们直接修改前面章节中使用的`testController.java`代码: **
```java
package com.spring.controller;

import com.spring.service.test.testService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping(value="/test")
public class testController {
    
	/** 利用@Autowired注解，注入testService实体对象，之后在testController中可以使用 私有属性service使用此对象 */
    @Autowired
    private testService service;
    
    @RequestMapping(value="/index", method = RequestMethod.GET)
    public String index(){
        System.out.println(service.getTestData("testController访问testService"));
        return "index";
    }
}
```

4.运行
----------------------------------
** 在SpringMVC目录下执行下面命令： **
```bash
mvn clean
mvn package
mvn tomcat:run
```
访问[http://localhost:8080/SpringMVC/test/index](http://localhost:8080/SpringMVC/test/index)！
控制台打印出以下字符：
`{"testController访问testService":"service返回的数据"}`

** 最简单的例子，没啥难点，但很实用！ **
