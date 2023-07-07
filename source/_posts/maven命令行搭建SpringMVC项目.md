---
title: Maven命令行搭建SpringMVC项目
date: '2017/02/23 15:04:00'
categories:
  - linux
  - maven
tags:
  - 软件
  - java
  - maven
abbrlink: 154af5c0
---
> 利用 MAVEN 构建 web 项目骨架

#### 建立包名为 `com.app`, 项目名为 `forfree`的普通JavaWeb项目
```bash
mvn archetype:generate -DgroupId=com.app -DartifactId=forfree -DarchetypeArtifactId=maven-archetype-webapp -DinteractivMode=false -DarchetypeCatalog=internal
```
#### 建立完成后，需要补全maven项目目录结构，添加以下目录：
./src/main/java
./src/test/java
./src/test/resources
./src/test/webapp
./src/test/webapp/WEB-INF
<!-- more -->

> 编辑pom.xml文件，引入spring框架及其相应依赖

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.app</groupId>
	<artifactId>forfree</artifactId>
	<packaging>war</packaging>
	<version>1.0</version>
	<name>forfree Maven Webapp</name>
	<url>http://maven.apache.org</url>
	
	<dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>4.1.4.RELEASE</version>
        </dependency>
 
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>4.1.4.RELEASE</version>
        </dependency>
 
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
            <scope>runtime</scope>
        </dependency>
 
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
            <scope>runtime</scope>
        </dependency>
    </dependencies>

	<build>
		<finalName>forfree</finalName>
	</build>
</project>

```
> 编辑`web.xml`文件，配置`servlet`运行环境

```xml
<!DOCTYPE web-app PUBLIC "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN" "http://java.sun.com/dtd/web-app_2_3.dtd" >
<web-app id="WebApp_ID" version="2.4" xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
    <display-name>Spring Web MVC Hello World Application</display-name>
    <servlet>
		<!-- 指定servler运行类 -->
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
			<!-- 指定spring servler运行配置文件的位置与名称 -->            
			<param-value>/WEB-INF/spring-servlet.xml</param-value>
        </init-param>    
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
</web-app>
```
> 在`/WEB-INF`目录下建立并编辑`spring-servlet.xml`文件，配置spring运行状态

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context" xmlns:mvc="http://www.springframework.org/schema/mvc" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.1.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.1.xsd">                    

    <!-- 给出扫描路径，spring将带有controller注解的类自动封装成bean -->
    <context:component-scan base-package="com.bash"/>

    <!-- don't handle the static resource -->
    <mvc:default-servlet-handler />

    <!-- if you use annotation you must configure following setting -->
    <mvc:annotation-driven />
    
    <!-- configure the InternalResourceViewResolver -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver" id="internalResourceViewResolver">
        <!-- 视图位置 -->
        <property name="prefix" value="/WEB-INF/views/" />
        <!-- 视图名后缀 -->
        <property name="suffix" value=".jsp" />
    </bean>
</beans>
```
> 建立控制器

#### 根据`spring-servlet.xml`中`context`给出的扫描路径，建立相应的包，并在其中建立控制器`testController.java`
```java
package com.bash;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
@Controller
@RequestMapping("/mvc")
public class testController {

    @RequestMapping("/hello")
    public String hello(){        
        return "hello";
    }
}
```

> 在`/WEB-INF/views`目录下建立视图文件`hello.jsp`

```jsp
<html>
<body>
<h2>Hello Spring</h2>
</body>
</html>

```
#### 至此，一个完整的SpringMVC的可运行环境已经搭建完成
>项目打包部署

```bash
mvn package
mvn tomcat:run
```

访问  [http://localhost:8080/forfree/mvc/hello](http://localhost:8080/forfree/mvc/hello "本地访问")  即可看到hello.jsp的内容了
