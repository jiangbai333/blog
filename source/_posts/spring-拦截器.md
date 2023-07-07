---
title: Spring-拦截器
date: '2017/04/11 09:06:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
  - spring拦截器
abbrlink: 5cc28b2a
---
** 先炒个栗子：你背着包去坐火车，进站之前，首先，会有人查你的票（这里相当于一层拦截器，拦截的目的是看你有没有进站的凭证）。OK，你的票通过了，然后你往里继续走，因为你背着包，所以还要过安检（这里是又一层拦截器，拦截的目的是看你有没有携带危险物品）。 **
** 上面的例子里，车站就相当于我们的spring应用，背着包要进车站的你就相当于用户请求，查票的拦截器，相当于察看用户凭证，映射到实际应用中就是，看你有没有登录，你没登陆（没有票）凭什么让你进入应用（车站）呢。而安检那里呢，就相当于拦截一些危险行为，比如，你在请求的参数里注入了一段清空数据表的sql（你的包里背了一个炸弹），那么肯定不能让你的请求进入应用！ **

<!-- more -->

** 下面的例子里，我们建立一个拦截器，放行所有视图渲染请求，拦截所有其它请求 **
1.添加拦截器配置
---------------------------
** 在`\src\main\resources\spring`目录下添加`applicationContext-hi.xml`文件： **
```xml
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
       
	<!-- interceptors里面放拦截器列表 -->
	<mvc:interceptors>
		<!-- interceptor里面代表一个拦截器 interceptors中可存在多个interceptor，每个interceptor将按照顺序执行 -->
		<mvc:interceptor>
			<!-- path="/**" 表示此拦截器将拦截所有请求 -->
			<mvc:mapping path="/**" />
			<!-- bean表示拦截器处理类 -->
			<bean class="com.spring.common.handlerInterceptor"></bean>
		</mvc:interceptor>
	</mvc:interceptors>
</beans>
```
** 建立完成后,需要在`applicationContext.xml`引用该配置，使其生效： **
`applicationContext.xml`:
```xml
<?xml version='1.0' encoding='UTF-8' ?>
<!-- was: <?xml version="1.0" encoding="UTF-8"?> -->
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">
       
    <import resource="classpath:spring/applicationContext-mvc.xml"/>
    <import resource="classpath:spring/applicationContext-hi.xml"/>
    <import resource="classpath:mybatis/mybatis.xml"/>
</beans>
```

2.在`\src\main\java\com\spring\common`目录下建立类`handlerInterceptor.java`
----------
** 前面提到过common目录是工具类目录，我们在这里把拦截器看作工具类 **
```java
package com.spring.common;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

//定义的拦截器需要实现 spring HandlerInterceptor 接口
public class handlerInterceptor implements HandlerInterceptor{
    
	/**
     * 这个方法将请求拦截在进入控制器之前，你可以用此方法，验证用户身份、权限等
     * @param request
     * @param response
     * @param handler
     * @return boolean 此方法如果返回为 true 则将请求发往控制器继续执行，如果返回 false 则拦截请求，结束该请求的生命周期
     * @throws Exception 
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
            Object handler) throws Exception {

		System.out.println("进入拦截器");
        String url = request.getRequestURI();
        return url.contains("display");
    }

    //这个方法将在视图渲染前进行拦截。
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response,
            Object handler, ModelAndView modelAndView) throws Exception {
        
        System.out.println("视图渲染前的拦截动作，要渲染的视图为：" + modelAndView.getViewName());
    }

    //这个方法，视图编译完成后执行（所谓编译完成，是指jsp文件、html文件等已经读取替换完成，等待返回前台渲染。）。可以在这里，执行一些统一的处理，例如日志写入等。
    @Override
    public void afterCompletion(HttpServletRequest request,
            HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        System.out.println("视图编译完成后执行的拦截动作");
    }
}

```
3.在`\src\main\java\com\spring\controller`目录下，建立控制器文件
------------------------
** 在这里，我们建立两个控制器文件，一个是用于视图渲染的`\view\viewController.java`另一个是用于返回数据的`\api\apiController.java` **
`viewController.java`:
```java
package com.spring.controller.view;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

/**
 *
 * @author hsoft
 */
@Controller
@RequestMapping(value="/display")
public class viewController {
    
    /**
     * 渲染视图
     * @param group 视图分组：指在page目录下的某一个分组目录
     * @param viewName 视图名：要渲染的视图文件名
     *      若要渲染page/menu目录下的view视图，则url为：/display/menu-view/
     *      若直接渲染page目录下的view视图，则url为：/display/-view/
     * @return 
     */
    @RequestMapping(value="/{view}", method = RequestMethod.GET)
    public String display(@PathVariable("view") String viewName){
        
        System.out.println("进入控制器");
        return viewName;
    }
}
```


`apiController.java`:
```java
package com.spring.controller.api;

import net.sf.json.JSONObject;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;
/**
 *
 * @author hsoft
 */

@Controller
@RequestMapping(value="/api")
public class apiController {
    
    @RequestMapping(value="/testData", method = RequestMethod.GET, produces = "text/html;charset=UTF-8")
    public @ResponseBody String testData(){
        return new JSONObject().accumulate("key", "中文").toString();
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

访问[http://localhost:8080/SpringMVC/display/index](http://localhost:8080/SpringMVC/display/index)时，将会渲染index.jsp文件的内容到浏览器，并且，tomcat控制台打印以下内容：
```bash
进入拦截器
进入控制器
视图渲染前的拦截动作，要渲染的视图为：index
视图编译完成后执行的拦截动作
```
从打印结果可以看出，请求是先进入拦截器的。并且，按照我们在`preHandle`中指定的规则，url中存在“display”关键字，我们认为这个一个视图渲染请求，将请求发送到控制器执行。

访问[http://localhost:8080/SpringMVC/api/testData](http://localhost:8080/SpringMVC/api/testData)时，浏览器会一片空白，并且，tomcat控制台只打印以下内容：
```bash
进入拦截器
```
因为我们的请求的url中，不存在“display”关键字，我们定义拦截器时，要求把这种请求拦截，因此，该请求并没有进入控制器，到拦截器就结束了它的生命周期。

至于拦截的实现，关键语句在于`preHandle`方法的返回值：`return url.contains("display");`，可以看出如果url存在“display”关键字会返回true，否则返回false。
