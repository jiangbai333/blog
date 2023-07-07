---
title: Maven命令行运行JavaWeb应用
date: '2016/12/01 14:04:00'
categories:
  - linux
  - maven
tags:
  - 软件
  - java
  - maven
abbrlink: 635e77de
---
之前一篇讲了如何利用[Maven命令行构建JavaWeb应用骨架](https://http://jiangbai333.github.io/2016/11/02/maven%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%9E%84%E5%BB%BAjavaweb%E5%BA%94%E7%94%A8/ "Maven命令行构建JavaWeb应用骨架")，现在说说如何利用构建好的Web骨架，建立一个完整的项目！  
## 目录结构
利用maven命令构造完JavaWeb项目骨架后，实际上目录结构是不全的，只是提供了src目录下的`main/resources`、`main/webapp`目录。之所以提供这两个目录是因为，这样可以构成一个jsp的运行结构。
如果想用java编写需要补全目录：
<!-- more -->

|目录					|目录用途				|
|:----------------------|:----------------------|
|src/main/java			|存放java代码的目录		|
|src/test/resources		|存放测试资源				|
|src/test/webapp		|存放测试web应用			|
|src/test/webapp/WEB-INF|存放测试web应用配置信息	|
|src/test/java			|存放测试java代码			|

将开发所必须的目录搭建好后即可以编辑代码进行开发了！
## 标准的servlet开发
在src/main/java目录下，建立test目录，并在目录中建立App.java文件，文件内容为：
```java
package test;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class App extends HttpServlet {

    public void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\">");
        out.println("<HTML>");
        out.println("  <HEAD><TITLE>A Servlet</TITLE></HEAD>");
        out.println("  <BODY>");
        out.print("    This is ");
        out.print(this.getClass());
        out.println(", using the GET method");
        out.println("  </BODY>");
        out.println("</HTML>");
        out.flush();
        out.close();
    }

    public void doPost(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<!DOCTYPE HTML PUBLIC \"-//W3C//DTD HTML 4.01 Transitional//EN\">");
        out.println("<HTML>");
        out.println("  <HEAD><TITLE>A Servlet</TITLE></HEAD>");
        out.println("  <BODY>");
        out.print("    This is ");
        out.print(this.getClass());
        out.println(", using the POST method");
        out.println("  </BODY>");
        out.println("</HTML>");
        out.flush();
        out.close();
    }
}
```
这是一个servlet入口类，我们还需要在web.xml中添加相应的映射用于访问：
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5"
        xmlns="http://java.sun.com/xml/ns/javaee"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
        http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
	<servlet>
		<description>This is the description of my J2EE component</description>
		<display-name>This is the display name of my J2EE component</display-name>
		<servlet-name>test</servlet-name>
		<servlet-class>test.App</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>test</servlet-name>
		<url-pattern>/index</url-pattern>
	</servlet-mapping>
	<welcome-file-list>
	<welcome-file>index.html</welcome-file>
	</welcome-file-list>
</web-app>
```
## maven 坐标配置
不利用maven开发的话，你需要自己去网上找servlet以及其相关所依赖的jar包，利用maven的最大好处就是，配好坐标，这些依赖类库就自动的引入到你的项目中了。
>修改项目主目录下的pom.xml，在`dependencies`标签中添加servlet坐标

```xml
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>servlet-api</artifactId>
	<version>2.5</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>javax.servlet.jsp</groupId>
	<artifactId>jsp-api</artifactId>
	<version>2.1</version>
	<scope>provided</scope>
</dependency>
<dependency>
	<groupId>javax.servlet</groupId>
	<artifactId>jstl</artifactId>
	<version>1.2</version>
</dependency>
```

## 打包、部署
在项目根目录下执行：
```base
mvn package
mvn tomcat:run
```
