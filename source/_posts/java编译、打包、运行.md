---
title: java编译、打包、运行
date: '2019/11/01 22:13:55'
categories:
  - 编程语言
  - java
  - 原生
  - 基础
tags:
  - java
  - 基础
abbrlink: '79897727'
---
# 相关命令介绍
## javac
> javac 是java语言编程编译器。全称java compiler。javac工具读由java语言编写的类和接口的定义，并将它们编译成字节代码的class文件。
> 引自----[百度百科](https://baike.baidu.com/item/javac/5156702?fr=aladdin)

### 使用方式
`javac [options][sourcefiles][@files]`（**参数排列顺序没有限定**）
<!-- more -->
### 命令行参数说明
参数|说明|实例
---|---|---
`-classpath`或`-cp`|设置用户的类路径，若不指定此项，则默认使用系统当前环境变量中的`CLASSPATH`与当前目录作为类路径，若指定此项，则会覆盖`CLASSPAHT`<br>`-classpath`指定类路径时，分隔符根据操作系统不同有所不同，类unix系统使用`:`作为分隔符，Windows系统使用`;`作为分隔符|`javac -classpath .:./libs:/root/libs @files`<br>`javac -cp .:./libs:/root/libs @files`
`-sourcepath`|要编译的类中需要的一些其它类（比如父类，变，但这些类定义不在要编译的类的源文件中，也没有在命令行列出，这时编译器可以自动到sourcepath指定的路径下去找这些类型的源文件，并自动编译|`javac -sourcepath ./src @files`
`-d`|指定编译后`.class`文件的输出目录|`javac -d target @files`
`-encoding`|设置被编译的源文件的编码格式，若不指定将使用当前编译平台的编码格式|`javac -encoding utf-8 @files`
`-verbose`|输出编译过程中的更详细信息|`javac -verbose @files`
`-g`|生成所有调试信息<br>衍生参数：`-g:{param1,param2}`<br>可选的传递值为：<br>`source`源文件调试信息（如不指定`-g`默认生成）<br>`lines`行号（如不指定`-g`默认生成）<br>`vars`局部变量<br>`-nowarn`禁用警告信息<br>`none`不生成调试信息|`javac -g @files`<br>`javac -g:none @files`<br>`javac -g:lines,vars @files`
`-deprecation`|显示每种不鼓励使用的API（包含成员和类）的使用或覆盖情况说明。没有给出 -deprecation 选项的话， javac 将显示这类源文件的名称：这些源文件使用或覆盖了不鼓励使用的API|`javac -deprecation @files`
## jar
> JAR文件是一种归档文件，以ZIP格式构建，以.jar为文件扩展名。
> 引自----[百度百科](https://baike.baidu.com/item/JAR/919533#viewPageContent)

### 使用方式
`jar {ctxui}[vfmn0PMe] [jar-file] [manifest-file] [entry-point] [-C dir] files`
### 命令行参数说明
参数|说明
---|---
-c|创建新档案
-t|列出档案目录
-x|从档案中提取指定的 (或所有) 文件
-u|更新现有档案
-v|在标准输出中生成详细输出
-f|指定档案文件名
-m|包含指定清单文件中的清单信息
-n|创建新档案后执行 Pack200 规范化
-e|指定应用程序入口点
-0|仅存储; 不使用任何 ZIP 压缩
-P|保留文件名中的前导 '/' (绝对路径) 和 ".." (父目录) 组件
-M|不创建条目的清单文件
-i|为指定的 jar 文件生成索引信息
-C|更改为指定的目录，仅针对此向后的第一个文件或目录
### 常用命令、参数组合形式
功能|命令
---|---
用一个单独的文件创建一个 JAR 文件|`jar -cf jarfile sourcefile...`
用一个目录创建一个 JAR 文件|`jar -cf jarfile dir`
创建一个未压缩的 JAR 文件|`jar -cf0 jarfile dir`
创建一个 JAR 文件的同时 指定程序主类入口|`jar -cfe jarfile package/classname sourcefile...`<br>`jar -cfe jarfile package.classname sourcefile...`
根据清单文件 创建一个 JAR 文件|`jar -cfm jarfile MANIFEST.MF dir`
指定jar命令运行的目录 创建一个 JAR 文件|`jar -cf jarfile -C path dir`<br>`jar -cf jarfile -C path dir1 -C path dir2`<br>`jar -cf jarfile -C path1 dir -C path2 dir`
更新一个 JAR 文件|`jar -uf jarfile sourcefile...`
查看一个 JAR 文件的内容|`jar -tf jarfile`
提取一个 JAR 文件的内容|`jar -xf jarfile`
从一个 JAR 文件中提取特定的文件|`jar -xf jarfile targetfile...`

## java


# 构建测试项目
&emsp;&emsp;为了测试引入第三方jar包进行编译打包，我们需要建立两个项目，一个当作主项目，一个充当第三方jar包。
## 第三方jar包项目
&emsp;&emsp;此项目最后将生产一个独立的jar，提供给主项目使用，用来测试外部jar打包。
### 目录结构
```bash
.
├── src
│   └── com
│       ├── ext.java
│       └── lib
│           ├── Dog.java
│           └── base
│               └── Animal.java
└── target
```
- target class文件输出位置
- src 源码位置
- com、lib、base 各个包

### 代码
#### ext.java
```java
package com;                                                                                                                                                                                            
                               
import com.lib.Dog;
import com.lib.base.Animal;    
 
public class ext {
    
    public static String mark = "外部类变量";
    
    public String method(String param) {
        return "使用了型参" + param + "和静态变量" + mark;
    }
 
    public Animal getDog() {
        return new Dog();
    }
}
```
#### Animal.java
```java
package com.lib.base;

public class Animal { }
```
#### Dog.java
```java
package com.lib;

import com.lib.base.Animal;

public class Dog extends Animal { }
```
## 主项目
### 目录结构
```bash
.
├── libs
│   └── ext.jar
├── src
│   └── com
│       └── Index.java
└── target
```
- target class文件输出位置
- src 源码位置
- libs 第三方jar包

### 代码
#### Index.java
```java
package com;                   
                               
import com.ext;     
import com.lib.base.Animal;                                                                                                                                                                             
                               
public class Index {
    public static void main(String[] args) {
                               
        ext e = new ext();     
        Animal animal = e.getDog();
                    
        System.out.println(ext.mark);   
        System.out.println(e.method("测试"));
        System.out.println(animal);
    }               
}   
```
# java项目的编译、打包与运行
&emsp;&emsp;我们将分别编译两个项目，生产`class`文件，运行包含`main`方法的主项目，打包两个项目，利用jar包运行主项目。在进行后续步骤前，先说几点注意事项：

1.上面的参数说明中已经说过了
> -classpath：设置用户的类路径，若不指定此项，则默认使用系统当前环境变量中的`CLASSPATH`与当前目录作为类路径，若指定此项，则会覆盖`CLASSPAHT`

用`javac`命令进行编译时，若未指定`classpath `，则其隐含的目录为当前目录，因此，在这种情况下，如果你在src的上级目录中执行编译命令，那么实际上编译器找的是`src/com/file`，但是你的源代码里的`package`却是以`com`开头，因此会提示找不到包。此时你必须将目录切换到`src`目录，与这样编译器就能直接找到`com`包。

2.通过文件列表指定被编译类时，不能使用通配符，需要写入所有需要编译的类。

3.打包时，如果项目需要单独运行（即包含主类），尽量提供`manifest.mf`文件，指定主类、依赖位置等。


## 编译、打包第三方jar包项目
&emsp;&emsp;由于这个项目仅仅为了提供给主项目测试第三方jar包，因此，不存在main方法，所以无法独立运行。
### 编译项目
#### 直接编译
&emsp;&emsp;按照注意事项 **1** 所说，因为`ext.java`所在的包是`com`，我们首先进入`src`目录，这时相当于我们能直接接触到`com`包，接下来运行如下命令：

```bash
javac com/ext.java com/lib/Dog.java com/lib/base/Animal.java
```
&emsp;&emsp;此时，`com`、`com/lib`、`com/lib/base`三个目录中分别出现了`ext.class`、`Dog.class`、`Animal.class`，证明已经编译成功。

&emsp;&emsp;当然，我们这个只是测试项目，实际开发是，项目内的文件数量远远不止三个，总不能都手写吧？实际上我们还可以使用另外四种指定文件的方法，与上面那种是等价的：

```bash
javac com/*.java com/lib/*.java com/lib/base/*.java
javac com/*
javac com/*.java
javac com/*/*.java
```

&emsp;&emsp;这里第二种和第三种差的不多，但是实际使用起来差别是比较大的，比如如果你的`com`目录下存在一些其它文件，例如目录、配置文件、json文件或者私钥认证文件，这时，用第二种方法会报错，第三种方法是在任何情况下都能使用的。
&emsp;&emsp;第四种方法可以解决源码目录下存在多个包的情况，比如我们后面的例子，在`src`目录下运行编译，但是与`com`包同级的还存在`org`包，此时就需要用第四种方法。
&emsp;&emsp;也许你会问，既然后面三种方式那么简洁，为什么还要说第一种？那是因为，有的时候你可能需要排除某些类不对其编译。
&emsp;&emsp;由于我们的测试项目均以`com`为主包，不存在同级包，因此为了方便，后面一律使用第三种方式。
#### 指定classpath与输出目录进行编译
&emsp;&emsp;细心的你可能发现了一点，上面的编译方式，虽然能编译成功，但是并不友好，首先就是生成的编译结果直接输出至源代码目录，其次就是在编译过程中需要切换目录，接下来我们来说解决方法。
&emsp;&emsp;除了上面的进入`src`目录进行编译的方式外，我们也可以通过指定`classpath`来在任意目录执行编译。假设我们现在处在`src`的上级目录中，那么当前编译命令相当于作用在src的同级目录，因此，在编译时，编译命令找到的是`src.com`包以及其子包中的类，这与源文件中的包名不同，我们可以通过`-classpath`参数，将编译器查找源文件的目录映射到`src`目录下，这样编译器就能找到与所指定源文件匹配的包`com`了。

```bash
javac -classpath src/ src/com/*.java
```
&emsp;&emsp;接下来，我们通过`-d`参数指定输出路径为`target`:

```bash
javac -d target/ -classpath src/ src/com/*.java
```
&emsp;&emsp;执行完成后，你可以看到，target目录下，生成了各个源文件的包路径及对应的`class`文件

```bash
target/
└── com
    ├── ext.class
    └── lib
        ├── Dog.class
        └── base
            └── Animal.class
```
#### 通过指定的文件列表编译
&emsp;&emsp;上面的编译方式，如果是小规模简单结构的项目还可以，但是如果是很大规模的项目可能就不是很方便，我们可以通过指定一个包含被编译源文件列表的文件（`file`）来编译项目，根据注意事项 **2** 针对此项目，文件`file`内容如下：

```bash
src/com/ext.java
src/com/lib/Dog.java
src/com/lib/base/Animal.java
```
&emsp;&emsp;执行以下命令，编译项目：

```bash
javac -d target/ -classpath src/ @file
```

&emsp;&emsp;编译结果与上面命令相同，也许你会觉得这样更复杂，其实不是，在实际开发过程中，可以通过自动化程序，去生成这个文件。
### 打包项目
&emsp;&emsp;jar包相当于一个压缩文件，将编译完成的`.class`文件打包起来，方便部署。我们对第三方`jar`的测试项目进行打包，输出`ext.jar`到`target`目录下。

```bash
# 进入target目录执行
jar -cvf ext.jar ./
jar -cvf ext.jar com/
jar -cvf ext.jar com/ext.class com/lib/Dog.class com/lib/base/Animal.class
# 在src目录下执行 将target目录下的class文件打包生成的ext.jar输出到target目录下
jar -cvf target/ext.jar -C target/ ./
jar -cvf target/ext.jar -C target/ com/
jar -cvf target/ext.jar -C target/ com/ext.class -C target com/lib/Dog.class -C target com/lib/base/Animal.class
```

## 编译、运行、打包主项目
&emsp;&emsp;我们的主项目，依赖`ext.jar`，因此我们先将`ext.jar`文件拷贝到主项目的`libs`目录中。
### 编译项目
#### 通过指定依赖jar包的位置进行编译
&emsp;&emsp;这种编译情况下，我们通过指定`ext.jar`（外部jar）位置，来进行编译

```bash
javac -d target -classpath ./src/:./libs/* src/com/*.java
```
#### 通过指定依赖源文件的位置进行编译
&emsp;&emsp;这种编译情况下，我们通过指定项目所依赖的外部类的源文件位置，来进行编译。

```bash
javac -d target -classpath ../extjar/src/:src/ src/com/*.java
javac -d target -sourcepath ../extjar/src/:src/ src/com/*.java
```

&emsp;&emsp;利用这种编译方式，被引用的依赖源文件也会同步编译到`target`目录中，因此在运行时，我们就不需要再去指定要引用的`jar`包了

### 运行项目
&emsp;&emsp;我们的主项目的`Index.java`文件时有`main`方法的，这也意味着，我们可有运行它输出一些结果，经过上面编译的步骤，我们已经获得了`class`文件，接下来我们来运行它们。
&emsp;&emsp;同编译一样，我们有两种方式运行项目，一种是通过制定外部依赖`jar`包的位置，另一种方式是直接通过在编译时指定的依赖源文件生成`class`文件运行.
&emsp;&emsp;指定依赖的`jar`包：

```bash
java -cp target/:libs/* com.Index
```
&emsp;&emsp;利用编译时指定的依赖源文件生成的`class`文件：
```bash
java -cp target/ com.Index
```
运行结果：
![图片无法显示](/images/java编译、打包、运行/jcpr.png)
### 打包项目、通过jar包运行项目
&emsp;&emsp;输出`project.jar`到`target`目录下。

```bash
# 进入target目录执行
jar -cvf ext.jar com/
# 在src目录下执行 将target目录下的class文件打包生成的ext.jar输出到target目录下 以下几种方式均可
jar -cvf target/ext.jar -C target/ ./
jar -cvf target/ext.jar -C target/ com/
jar -cvf target/ext.jar -C target/ com/Index.class

cd target && jar -cvf ext.jar ./
cd target && jar -cvf ext.jar com/
cd target && jar -cvf ext.jar com/Index.class
```
&emsp;&emsp;打包完成后我们通过`java`命令运行项目，由于项目依赖`ext.jar`，因此需要利用`-classpath`参数指定依赖路径:

```bash
java -classpath target/index.jar:libs/* com.Index
```
&emsp;&emsp;通过运行的命令可以看得出来，仅仅打包生成的class文件时，在运行时需要利用`-classpath`指出依赖所在位置。
&emsp;&emsp;注意事项 **3** 中我们提到了`manifest.mf`文件，在这种打包方式下，我们需要指定一个`manifest.mf`清单文件，注意它的格式，以`key-value`形式指出每一项，`value`项前面需要有空格，同一`value`中有多个值时，各个值之间需要有空格。我们在target目录下建立`META-INF`目录，在其中添加`MANIFEST.MF`文件，内容如下：

```vim
Manifest-Version: 1.0
Class-Path: ../libs/ext.jar
Main-Class: com.Index
```
&emsp;&emsp;接下来打包文件：

```bash
jar -cvfm target/project.jar target/META-INF/MANIFEST.MF -C target/ ./ 
```
&emsp;&emsp;执行完成后，会在target目录下生成`project.jar`，我们通过`java -jar`命令即可运行它

```bash
java -jar target/project.jar

外部类变量
使用了型参{测试}和静态变量{外部类变量}
com.lib.Dog@2503dbd3
```
