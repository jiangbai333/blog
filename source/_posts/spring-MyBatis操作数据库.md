---
title: Spring-MyBatis操作数据库
date: '2017/04/08 15:33:00'
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
  - 数据库
abbrlink: 2f4c9815
---
1.建立dao层接口`baseDao.java`与实现类`testDaoImpl.java`
---------------------------
** 在`\src\main\java\com\spring\dao`目录下，建立`ifc`、`impl`目录，分别用来存放接口和实现类，在`ifc`目录中建立`baseDao.java`接口文件，在`impl`目录中建立`testDaoImpl.java`实现类文件； **

<!-- more -->

`baseDao.java`文件：
```java
package com.spring.dao.ifc;

import java.util.List;
import java.util.Map;
import net.sf.json.JSONObject;

public interface baseDao {
    public List<Map<String, Object>> getList(JSONObject jsonObject);
}
```
`testDaoImpl.java`文件：
```java
package com.spring.dao.impl.test;

import com.spring.dao.ifc.baseDao;
import java.util.List;
import java.util.Map;
import net.sf.json.JSONObject;
import org.mybatis.spring.SqlSessionTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

@Repository
public class testDaoImpl implements baseDao {
    @Autowired
    private SqlSessionTemplate sqlSessionTemplate;
	
	/**
     * 数据库查询方法
     * @param jsonObject 以键值对形式传入sql供Sql模板文件解析执行
     *      @数据格式：{"sql":"需要执行的sql语句"}
     *      @数据格式：{"field":"要查询的字段", "table":"要查询的表名", "where":"查询语句where条件"}
     *      两种格式选择一种，这实际上是我根据上篇文章中提到的initDaoMapper.xml文件格式而定的，你也可以自己写！
     * @return List<Map<String, Object>> 查询结果集
     */
    @Override
    public List<Map<String, Object>> getList(JSONObject jsonObject) {
        return sqlSessionTemplate.selectList("TestDao.getList", jsonObject);
    }
}
```

2.修改`testService.java`
---------------------------
```java
package com.spring.service.test;

import com.spring.dao.impl.test.testDaoImpl;
import net.sf.json.JSONArray;
import net.sf.json.JSONObject;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class testService {
    
	//注入DAO
    @Autowired
    private testDaoImpl testDaoHand;
    
	//这个例子里，getTestData的参数key没有用
    public String getTestData(String key) {
        
		JSONObject jo = new JSONObject();
		
		/** 构造Sql模板需要使用的JSONObject */
        jo.accumulate("sql", "select * from user");
		/** 下面的形式与上面的作用是等价的 */
		//jo.accumulate("table", "user");
        
		JSONArray jr = JSONArray.fromObject(testDaoHand.getList(jo));
        return jr.toString();
    }
}
```

3.建立数据表，并写入一些测试数据
----------------------------------
`user.sql`:
```sql
-- ----------------------------
-- Table structure for user
-- ----------------------------
DROP TABLE IF EXISTS `user`;
CREATE TABLE `user` (
  `username` varchar(15) NOT NULL,
  `tel` varchar(15) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
-- Records of user
-- ----------------------------
INSERT INTO `user` VALUES ('你', '156xxxx1501');
INSERT INTO `user` VALUES ('我', '858xxxx5858');
INSERT INTO `user` VALUES ('他', '123xxxx3213');
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
`[{"username":"你","tel":"156xxxx1501"},{"username":"我","tel":"858xxxx5858"},{"username":"他","tel":"123xxxx3213"}]`
5.关于增、删、改
-----------------------------------
** 其实归根到底，mybatis只是相当于在你与数据库之间构建起一道桥梁，你将你需要执行的sql利用mybatis通过mapper构建出来，再由mybatis发往数据库执行。这个例子里虽然只用到了查询，其实，你只要自己在mapper的xml文件里定义好增、删、改的sql模板，跟这个一样，传进去模板参数就执行了； **
