---
title: Spring-引用静态资源
date: '2017/07/06 14:21:00'
categories:
  - 编程语言
  - java
  - 框架
  - SpringMVC
tags:
  - java
  - 框架
  - SpringMVC
abbrlink: 644af617
---
为什么要使用静态资源
----
##### 在团队开发过程中，组员各有分工。在编写代码过程中要尽量避免冲突，要尽量将各类代码分离。因此在视图文件中，要尽量抽离出`javascript`、`css`代码到外部文件，然后按照`静态资源`进行引用（以上废话为了凑字，可无视）。

<!-- more -->

配置静态资源路径
----
** 修改`src\main\resources\spring\applicationContext-mvc.xml`文件，添加静态资源引用路径: **
```xml
<!-- 添加静态资源映射 --
<mvc:resources mapping="/resources/**" location="/WEB-INF/resources/" />
<mvc:resources mapping="/images/**" location="/WEB-INF/images/" />
<mvc:resources mapping="/js/**" location="/WEB-INF/js/" />
```

引用静态资源
----
** 配置好静态资源路劲后，可在`jsp`、`html`等视图页中引用资源。 **
以`echarts`库为例，将下载好的`echarts.min.js`文件放置在`src\main\webapp\WEB-INF\resources\javascript`目录下，编辑视图文件引用`echarts`:
```html
<%@page contentType="text/html" pageEncoding="UTF-8"%>
<%
String path = request.getContextPath();
String basePath = request.getScheme()+"://"+request.getServerName()+":"+request.getServerPort()+path+"/";
%>
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
   "http://www.w3.org/TR/html4/loose.dtd">

<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
        <script src="<%=basePath %>resources/javascript/chart/echarts.min.js"></script>
        <title>JSP Page</title>
    </head>
    <body>
        <h1>Hello World!</h1>
        <div id="main" style="width: 600px;height:400px;"></div>
            <script type="text/javascript">
                // 基于准备好的dom，初始化echarts实例
                var myChart = echarts.init(document.getElementById('main'));

                // 指定图表的配置项和数据
                var option = {
                    title: {
                        text: 'ECharts 入门示例'
                    },
                    tooltip: {},
                    legend: {
                        data:['销量']
                    },
                    xAxis: {
                        data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
                    },
                    yAxis: {},
                    series: [{
                        name: '销量',
                        type: 'bar',
                        data: [5, 20, 36, 10, 10, 20]
                    }]
                };

                // 使用刚指定的配置项和数据显示图表。
                myChart.setOption(option);
            </script>
    </body>
</html>

```

