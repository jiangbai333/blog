---
title: forfreej0.3 文档
categories:
  - java
  - forfreej
tags:
  - 框架
  - MVC
  - 教程
abbrlink: 44d8d267
date: 2016-06-07 11:56:55
---
# ForFreej 0.3#
####框架结构
----
**src目录下 包含了 app包、com包以及程序人员开发时需要的配置文件。**

**app包是开人人员部署自己项目代码用的。**

**com包是存放维持forfreej框架运行的核心代码的程序包，如果你有很好的代码阅读能力并且有一定java语言基础，你可以随意修改。但某些不规范或者不正确的修改，将导致forfreej我发正常运行！**


<!-- more -->

![](./colortree.png)


####运用forfreej快速建立一个web应用
----
## 首先为应用建立一个index控制器(IndexController.java): ##
#####建立控制器需要符合 forfreej 要求的准则：每一个控制器必须属于某一分组、控制器需要继承核心控制器、控制器命名为 控制器名Controller.java!
根据上面的要求，首先在 app 包下控制器容器(app.controller)中建立一个分组包，分组包名为 auto。然后在 auto 包中建立控制器IndexController.java,控制器代码如下：

    package app.controller.auto;

	import java.util.HashMap;
	import java.util.Map;

	import com.sys.core.controller.ControllerBase;
	
	/**
	 * 建立控制器类，继承核心控制器 ControllerBase
	 */
	public class IndexController extends ControllerBase {
	
		//二级模板变量，设置后，将在视图页面替换相应的模板变量
		public Map<String, String> title = new HashMap<String, String>(){{
			put("head", "一个测试页面");
			put("body", "forfreej0.3");
		}};
	
		//一级模板变量，设置后，将在视图页面替换相应的模板变量
		public String str = "极致简约! 开发由我";
	
		/**
		 * 控制器动作，这里写需要执行的逻辑代码
		 */
		public void test() {
		 	/** 视图渲染方法 */
			this.display();
		}
    }

##为控制器建立视图文件
控制器建立完成之后，由于这个例子是渲染一个视图，所以需要在 view 层中建立一个 html 视图文件。建立视图文件时需要注意，需要将视图文件放入与控制器分组包名相同的文件夹中，并且视图名以控制器名称进行命名。

因此，首先请在 webRoot 下建立 auto 文件夹，并在 auto 分组下建立 index.html 文件，代码如下：

    <html>
		<head>
			<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
			<title>
				<!-- 通过二级模板变量引用  title.head 字符串("一个测试页面")-->
				{$title.head}
			</title> 
		</head>
		<body>
			<h1>
				<!-- 通过二级模板变量引用  title.head 字符串("forfreej0.3")-->
				{$title.body}
			</h1>
			<h2>
				<!-- 通过一级模板变量引用  str 字符串("极致简约! 开发由我")-->
				{$str}
			</h2>
		</body>
	</html>
    

#####至此，一个最简单的web应用就开发完成了,打开浏览器，访问请输入URL:
**http://localhost:端口号/部署的项目名称/index?p=auto&c=index&a=test**



####数据库操作
----



####forfreej0.4版本 更新内容
----
1. 优化了模板引擎解析效率，增加了二级模板变量解析功能
2. 优化 forfreej 核心代码，着重处理了线程安全问题
3. 添加数据库操作类
4. 添加配置文件操作类
5. 增加redis数据库操作类

