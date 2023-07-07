---
title: javadoc生成项目文档
date: '2019/10/08 11:39:53'
categories:
  - 软件
  - javadoc
tags:
  - java
  - doc
abbrlink: 8c625a34
---

# 简介
------
> javadoc是Sun公司提供的一个技术，它从程序源代码中抽取类、方法、成员等注释形成一个和源代码配套的API帮助文档。也就是说，只要在编写程序时以一套特定的标签作注释，在程序编写完成后，通过Javadoc就可以同时形成程序的开发文档了。
> 引自----[百度百科](https://baike.baidu.com/item/javadoc/4640765?fr=aladdin)

<!-- more -->

# 文档注解说明
标签|说明|标示范围
---|---|---
@author|作者|包、接口、类
@version|版本号|包、 类、接口
@param|方法的入参名及描述信息，如入参有特别要求，可在此注释。|构造函数、 方法
@return|方法返回值| 方法
@deprecated|标识随着程序版本的提升，当前API已经过期，仅为了保证兼容性依然存在，以此告之开发者不应再用这个API。|包、类、接口、值域、构造函数、 方法
@throws|构造函数或方法所会抛出的异常。|构造函数、 方法
@exception|同@throws。| 构造函数、 方法
{@see package.class#method}|查看相关内容，如类、方法、变量等。<br><br>引用当前类<br>@see #field<br>@see #method(Type, Type, ...)<br>@see #method(Type argname, Type argname, ...)<br><br>引用当前包或倒入包中的类<br>@see class<br>@see class#field<br>@see class#method(Type, Type, ...)<br>@see class#method(Type argname, Type argname, ...)<br><br>引用其它包<br>@see package<br>@see package.class<br>@see package.class#field<br>@see package.class#method(Type, Type, ...)<br>@see package.class#method(Type argname, Type argname, ...)|包、类、接口、值域、构造函数、 方法
@since|API在什么程序的什么版本后开发支持。|包、类、接口、值域、构造函数、 方法
{@link package.class#method}|链接到某个特定的成员对应的文档中。与`@see`相同，但`@see`必须在开头，`@link`则不必|包、类、接口、值域、构造函数、 方法
{@value}|当对常量进行注释时，如果想将其值包含在文档中，则通过该标签来引用常量的值。|静态值域

# javadoc命令常用参数说明
参数|说明|使用详情
---|---|---
-d <路径>|指定文档输出位置，生成的doc文档将输出到该目录下|可为相对路径或绝对路径
-windowtitle|指定doc文档在浏览器标签栏中显示的标题|
-doctitle|指定在文档首页显示的标题|
-header|指定文档页左上角显示的标题|
-public|仅显示公共类和成员|
-protected|显示受保护/公共类和成员|默认
-package|显示软件包/受保护/公共类和成员|
-private|显示所有类和成员|
-link|引用外部文档<br>`javadoc -link https://docs.oracle.com/javase/8/docs/api/ package`|
-group|分组<br>`java -group "核心包" "package1.package2*:package3" -group "扩展包" "package4"`|
-sourcepath <路径列表>|指定查找源文件的位置|类UNIX系统以`:`分割，windows系统以`;`分割
-classpath <路径列表>|指定查找用户类文件的位置|类UNIX系统以`:`分割，windows系统以`;`分割
-exclude <软件包列表>|指定要排除的软件包的列表|以`:`分割
-subpackages <子软件包列表>|指定要递归装入的子软件包|以`:`分割
-verbose|显示详细生成信息

需要获取更详细的参数说明可以到这里[Sun官方说明](https://docs.oracle.com/javase/10/tools/javadoc.htm#JSWOR-GUID-9D532574-1CDB-4D30-99F3-A308DCAEE55F)

# 实例代码
## 目录结构及测试代码
### 测试用实例代码目录结构
```bash
└── src
    └── com
        ├── Index.java
        ├── impl
        │   ├── AddService.java
        │   └── ext
        │       └── AddExt.java
        └── inf
            └── AddInterface.java
```
### Index.java
```java
package com;

import java.util.List;
import java.util.ArrayList;

/**
 * Index类
 * <p style='color:red'>测试入口类</p>
 * 可用p标签包裹注释内容 形成段落 实现换行
 * 可利用style属性设置样式 如颜色、字体等
 * 注释在类上 生成类的相关doc文档
 */
public class Index {

    /* 文件内部注释 不会生成在doc文档中 */
    public String field1;

    /** 文档注释 可生成在doc文档中 */
    public static List field2;

    /**
     * 静态代码块
     * 不会生成在doc文档中
     */
    static {
        field1 = new ArrayList();
    }

    /**
     * 非静态代码块
     * 不会生成在doc文档中
     */
    {
        field1 = new ArrayList();
    }

    /**
     * main方法
     * 返回值为void时不可指定{@code @return}
     *
     * @param args String[] 参数注释
     */
    public static void main(String[] args) { }
}
```
### AddExt.java
```java
package com.impl.ext;

import java.io.IOException;

import com.impl.AddService;

/**
 * 测试继承
 * 注释在类上 为类生成相应的doc文档
 * 可以利用<b>pre</b>标签与{@code {@code codes}}组合
 * 在doc中生成代码示例 
 *
 * <pre>
 * {@code
 * List<AddExt> aeList = new ArrayList<AddExt>();
 * System.out.println(aelist);
 * }
 * </pre>
 *
 * 若不实用pre标签 则注释中的代码不会换行 均以一行显
 * 示 效果如下
 *
 * {@code
 * List<AddExt> aeList = new ArrayList<AddExt>();
 * System.out.println(aelist);
 * }
 */
public class AddExt extends AddService {

    /**
     * add方法
     * 注释在方法上 为方法生成相应的doc文档
     * @param a int
     * @param b int
     * @param c int
     * @exception IOException 
     * 抛出异常
     * @return int
     * @see #field1
     * @see #add(int, int)
     * @see #add(int a, int b)
     * @see AddService
     * @see AddService#field1
     * @see AddService#add(int a, int b)
     * @see com
     * @see com.Index
     * @see com.Index#field1 
     */
    public int add(int a, int b, int c) throws IOException {
        return a + b + c;
    }    

    /**
     * 重写的add方法
     * <p>通过添加注释信息 覆盖父类注释 生成新的doc文档注释</p>
     *
     * @param a int
     * @param b int
     * @return int
     */
    public int add(int a, int b) {
        return a + b;
    }

    public String toString() {return null;}
}
```
### AddService.java
```java
package com.impl;

import com.inf.AddInterface;

public class AddService implements AddInterface {

    /** 公共属性 */
    public String field1;
    /** 保护属性 */
    protected String field2;
    /** 私有属性 */
    private String field3;

    @Override
    public int add(int a, int b) {
        return a + b;
    }

    /**
     * 子类或实现类重写或实现方法时 不写
     * 新的注释 将直接将父类或接口重的doc
     * 注释复制过来
     */
    public String toString() {return null;}
}
```
### AddInterface.java
```java
package com.inf;

/**
 * <p>测试接口</p>
 */
public interface AddInterface {
    
    /**
     * <p>测试方法</p>
     * 
     * @param a number a
     * @param b number b
     * @return sum of a and b
     */ 
    int add(int a, int b);
}
```
# 生成方式
需在包目录下执行此命令，我们的例子中需要在`src`目录下执行
## 指定所有包
`javadoc -private -d ../doc -header 测试 -link https://docs.oracle.com/javase/8/docs/api/ -group "core" "com.impl*:com.inf" -group "ext" "com" com com/*/*.java com/*/*/*.java`
## 指定包列表文件
### packages文件
```base
com
com.impl
com.impl.ext
com.inf
```
`javadoc -private -d ../doc -header 测试 -link https://docs.oracle.com/javase/8/docs/api/ -group "core" "com.impl*:com.inf" -group "ext" "com" @packages`
# 添加包的doc说明
以上方式添加的文档说明，无法应用到包上，如果需要给包添加doc文档，需要建立一个`package-info.java`放置在相应的包下，我们以`com`包为例，`package-info.java`内容如下：

```java
/**            
 * 提供包的doc注释
 * @since 1.0  
 * @author Forfree
 * @version 1.0                                                                                                                                                     
 */            
package com; 
```
将此文件放置在`com`包下执行`javadoc`命令即可。
# 为部分类生成javadoc文档
在执行`javadoc`命令时，只需要在结尾指定需要生成文档的类所在的包名和类名即可。
