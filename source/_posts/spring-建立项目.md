---
title: Spring-建立项目
date: '2017/04/03 10:10:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
abbrlink: de85bed
---
1.首先建立一个基本的maven项目 
---------------------------
> Maven命令行构建JavaWeb应用--参数解释 [引自jiangbai333.github.io](http://jiangbai333.github.io/2016/11/02/maven%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%9E%84%E5%BB%BAjavaweb%E5%BA%94%E7%94%A8/ "引自jiangbai333.github.io")

<!-- more -->

```bash
mvn archetype:generate -DgroupId=com.spring -DartifactId=SpringMVC -DarchetypeArtifactId=maven-archetype-webapp -DinteractivMode=false -DarchetypeCatalog=internal
```

** 运行上面命令后，将在当前目录建立一个名称为`SpringMVC`的maven项目目录,结构如下： **
```
└─src
	└─main
    	├─resources
        └─webapp
        	└─WEB-INF
```
2.构建maven项目骨架
---------------------------
** 上面并不是一个完整的maven项目骨架，接下来我们手动完成项目骨架，将目录补全为： **
```
└─src
	├─main
	│	├─java
	│	│	└─com
	│	│   	└─spring
	│	│       	├─common
	│	│           ├─controller
	│	│           ├─dao
	│	│           ├─mbo
	│	│           └─service
	│	├─resources
	│	│	├─mybatis
	│	│	│	└─mbo
	│	│	└─spring
	│	└─webapp
	│   	├─META-INF
	│       └─WEB-INF
	│       	└─page
	└─test
```

** 目录功能如下 **
- main 项目主目录
	- java.com.spring
		* common 存放公共的类
		* controller 存放控制器类
		* service 存放业务逻辑类
		* dao 存放数据操作类
	- resources 资源目录
		* spring spring配置文件
		* mybatis mybatis配置文件
			* mbo 存放 mybatis Sql映射文件
	- webapp 存放web应用源代码xml、html、jsp等
		* META-INF
		* WEB-INF
			* page 
- test maven 测试目录 结构与main一致即可		

** 应该说明一点，各个目录的功能，并不是由目录名字决定的。之所以这样命名，并且“赋予相应的功能”只是为了达成一个共同的开发标准。这对团队开发是非常重要的！如果你开心，完全可以用service目录存放控制器，controller目录存放业务逻辑，甚至把所以文件都放在同一个目录下！ **
3.补全pom.xml文件，利用maven添加spring依赖
---------------------------
`pom.xml` 位于项目根目录（\SpringMVC）
```bash
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>SpringMVC</groupId>
  <!-- 这里用来项目运行名 -->
  <artifactId>SpringMVC</artifactId> 
  <version>0.0.1-SNAPSHOT</version>
  <packaging>war</packaging>
  <name>SpringMVC</name>
  <description/>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <spring.version>4.1.6.RELEASE</spring.version>
  </properties>
  <dependencies>
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.1</version>
    </dependency>
    <!-- spring用到的包 -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-web</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
      <type>jar</type>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>${spring.version}</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-tx</artifactId>
      <version>${spring.version}</version>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>2.3.2</version>
        <configuration>
          <source>1.7</source>
          <target>1.7</target>
        </configuration>
      </plugin>
      <plugin>
        <artifactId>maven-war-plugin</artifactId>
        <version>2.2</version>
        <configuration>
          <version>3.1</version>
          <failOnMissingWebXml>false</failOnMissingWebXml>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```
4.编辑servlet运行配置、spring框架运行配置
---------------------------
#### `web.xml`：servlet运行配置，存放于`SpringMVC\src\main\webapp\WEB-INF\`目录下
```bash
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="3.0" xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd">
    <display-name>springmvc</display-name>

    <servlet>
		<!-- 配置一个servlet 名称为springmvc 交由springframework的DispatcherServlet类处理 -->
        <servlet-name>springmvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<!-- 配置一对运行参数，spring在取得控制权后，会根据这里寻找spring运行配置文件 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/applicationContext.xml</param-value>
        </init-param>
		<!-- 配置servlet装载顺序 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
		<!-- 名为springmvc的servlet将拦截基于“/”的所有请求，并取得控制权 -->
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
		<!-- 如果你有多个servlet，可在此处依次进行配置 -->
    </servlet-mapping>

    <filter>
		<!-- 指定一个过滤器，并交由springframework的CharacterEncodingFilter类处理 -->
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<!-- 这里相当于springframework处理过滤信息时的两个配置 -->
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
		<!-- 让encodingFilter过滤基于“/*”的全部请求 -->
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <session-config>
        <session-timeout>60</session-timeout>
    </session-config>
</web-app>
```
#### `applicationContext.xml`：spring主配置文件，存放于`SpringMVC\src\main\resources\spring\`目录下
> Spring ApplicationContext容器介绍  [引自极客学院wiki](http://wiki.jikexueyuan.com/project/spring/ioc-container/spring-application-context-container.html "引自极客学院wiki")
> applicationContext.xml模块化处理spring配置文件 [引自360doc](http://www.360doc.com/content/14/0406/00/7669533_366687154.shtml "引自360doc")

```bash
<?xml version='1.0' encoding='UTF-8' ?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
    
	<!-- 引入子配置文件 -->  
    <import resource="classpath:spring/applicationContext-mvc.xml"/>
</beans>
```
#### `applicationContext-mvc.xml`：spring子配置文件，存放于`SpringMVC\src\main\resources\spring\`目录下
> Spring MVC静态资源处理 [引自"行在途中"个人博客](http://www.cnblogs.com/fangqi/archive/2012/10/28/2743108.html "引自"行在途中"个人博客")
> SpringMVC初探视图解析器 [引自"夏日的雪花"个人博客](http://www.cnblogs.com/liruiloveparents/p/5054605.html "引自"夏日的雪花"个人博客")
> SrpingMVC mvc:annotation-driven 标签 [引自"starscream"个人博客](http://starscream.iteye.com/blog/1098880 "引自"starscream"个人博客")

```bash
<?xml version='1.0' encoding='UTF-8' ?>
<!-- was: <?xml version="1.0" encoding="UTF-8"?> -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
       http://www.springframework.org/schema/context 
       http://www.springframework.org/schema/context/spring-context-3.0.xsd 
       http://www.springframework.org/schema/mvc 
       http://www.springframework.org/schema/mvc/spring-mvc.xsd"
       default-lazy-init="true">
    
	<!-- 配置spring组件扫描位置，com.spring包下的所有类文件将被spring标识为注解注入类，若存在注解则自动进行注入 -->
    <context:component-scan base-package="com.spring"/>
	<!-- 将静态资源交由spring处理(详细内容见引用部分) -->
    <mvc:resources location="/WEB-INF/pages/" mapping="/page/**"/>
    <!-- 配置视图由spring解析器InternalResourceViewResolver解析(详细内容见引用部分) -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/page/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <!-- 默认的注解映射的支持(详细内容见引用部分) -->
    <mvc:annotation-driven>
        <mvc:message-converters register-defaults="true">
            <bean class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
                <property name="supportedMediaTypes">
                    <list>
                        <value>text/plain;charset=utf-8</value>
                        <value>application/json;charset=utf-8</value>
                        <value>application/x-www-form-urlencoded</value>
                    </list>
                </property>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
</beans>
```

** 至此，一个springmvc开发环境已经搭建完毕 **
