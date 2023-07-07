---
title: Spring-整合MyBatis
date: '2017/04/08 10:50:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
  - MyBatis
abbrlink: 614048ea
---
![图片无法显示](spring.jpg)
> mybatis简介 [mybatis.org](http://www.mybatis.org/mybatis-3/zh/ "mybatis.org")
> MyBatis [百度百科](http://baike.baidu.com/link?url=FDonLHL0N0Cg9ZscQYpJxEcAfxE7G7Nh3RM7UNdewR_lsh6UcTyf2uoxurvwmMWSrDhYwWdfJY7X0LVBGLLLja "百度百科")

<!-- more -->

1.准备工作
---------------------------
** 修改pom.xml，添加mybatis相关的依赖 **
```bash
<!--数据库相关, mysql, mybatis-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.37</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>${mybatis-spring-version}</version>
</dependency>
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>${mybatis-version}</version>
</dependency>
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.2</version>
</dependency>
```

2.编写mybatis配置文件、数据库连接配置文件
---------------------------
** 在`\src\main\resources\mybatis`目录下建立mybatis配置文件`mybatis.xml`，内容如下： **
```bash
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:beans="http://www.springframework.org/schema/beans" xmlns:context="http://www.springframework.org/schema/context" default-autowire="byName" xmlns:tx="http://www.springframework.org/schema/tx" xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.1.xsd">
	
    <!-- 数据库JDBC的配置（host、数据库名、用户名、密码啥的） --> 
    <context:property-placeholder location="classpath:/mybatis/jdbc.properties" ignore-unresolvable="true"/>
    
    <!-- 配置一个id为dataSource的数据源 -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName"><value>${jdbc.driverClassName}</value></property>
        <property name="url"><value>${jdbc.url}</value></property>
        <property name="username"><value>${jdbc.username}</value></property>
        <property name="password"><value>${jdbc.password}</value></property>
    </bean>
        
    <!-- 为dataSource数据源开启事务处理 -->
    <tx:annotation-driven transaction-manager="transactionManager" />
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource" />
    </bean> 
	

	<!-- 以下位置注释需要补充 -->
	<!-- ---------------------------------------------------------------------- -->
    <bean id="sqlSessionFactory"  class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!-- mybatis配置文件路径(这里的“mybatis”配置文件是指真正的只针对mybatis的配置文件，与此文件不同，此文件是spring整合mybatis时的配置文件，实际属于spring的配置) -->
        <property name="configLocation"  value=""/>
        <!-- Sql模板文件的路径 -->
        <property name="mapperLocations" value="classpath*:mybatis/mbo/**/*.xml"/>
    </bean>
    
    <!-- 配置sqlsession 通过sqlSessionFactory实体 产生sqlsessionTemplate -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg index="0">
            <ref  bean="sqlSessionFactory"/>
        </constructor-arg>
    </bean>
</beans>

```
** 编写完配置文件后，我们需要在spring主配置文件中引入此配置文件，使该配置生效。修改`\src\main\resources\applicationContext.xml`： **
```bash
<import resource="classpath:spring/applicationContext-mvc.xml"/>

<!-- 上面一行是前文中说过的，引入的spring配置，下面是新添加的mybatis配置 -->
<import resource="classpath:mybatis/mybatis.xml"/>
```
** 既然我们要连接数据库，操作数据库中的数据，那么就的有相应的数据库给mybatis连！接下来写一个数据库连接配置文件！在`\src\main\resources\mybatis`目录下建立`jdbc.properties`文件，内容如下： **
```bash
# JDBC驱动
jdbc.driverClassName=com.mysql.jdbc.Driver  
# host为地址，如果是本机可用localhos或127.0.0.1；port是端口号，mysql默认3306；database是要连接的数据库
jdbc.url=jdbc:mysql://host:port/database
# 用户名
jdbc.username=root  
# 密码
jdbc.password=root
```
** 这里注意，`jdbc.properties`文件里的配置格式需要与上面`mybatis.xml`文件中`<bean id="dataSource" ...>`标签中应用配置的格式对应上：  **
```bash
<!-- `mybatis.xml`文件中 引用方式,${jdbc.*}-->
<bean id="dataSource"  ...>
    <property name="driverClassName"><value>${jdbc.driverClassName}</value></property>
    <property name="url"><value>${jdbc.url}</value></property>
    <property name="username"><value>${jdbc.username}</value></property>
    <property name="password"><value>${jdbc.password}</value></property>
</bean>
```

3.建立Sql模板文件
----------------------------------
** 在`\src\main\resources\mybatis\mbo`目录下，建立模板文件，可以将不同功能的sql模板文件分目录存放，这样更好管理；在`\src\main\resources\mybatis\mbo`目录下建立`initDaoTemp`目录,并在其中建立`initDaoMapper.xml`文件，内容如下： **
```bash
<?xml version="1.0" encoding="UTF-8" ?>    
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace="initSelectTemp" 是给这个mapper标签起个名字，下一篇文中中，我们将利用这个名字引用这个mapper中的模板，进行数据库读取操作 -->
<mapper namespace="initSelectTemp">	
    <select id="getList" resultType="java.util.Map" parameterType="net.sf.json.JSONObject" >
        <choose>
            <when test="sql != null and sql != ''">
                ${sql}
            </when>
            <otherwise>
                select 
                <choose>
                    <when test="field != null and field != ''">
                        ${field}
                    </when>
                    <otherwise>
                        *
                    </otherwise>
                </choose>	
                from 
                <if test="table != null and table != '' ">
                    `${table}`
                </if>
                <if test="where != null and where != '' ">
                    where  ${where} 
                </if>
            </otherwise>
        </choose>
    </select>
</mapper>
```
** 至此，spring mybatis整合完成 **
