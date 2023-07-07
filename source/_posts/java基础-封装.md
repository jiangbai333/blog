---
title: java基础-封装
date: '2018/11/12 21:01:00'
categories:
  - 编程语言
  - java
  - 原生
  - 基础
tags:
  - java
  - 基础
abbrlink: 5db0650a
---

# 简介
------
&emsp;&emsp;封装是面向对象编程的三大特性之一，java作为一门面向对象的语言，在开发过程中，进行完善的对象封装可提升软件的健壮性与可维护性。
> 确保组件不会以不可预期的方式改变其它组件的内部状态；只有在那些提供了内部状态改变方法的组件中，才可以访问其内部状态。每类组件都提供了一个与其它组件联系的接口，并规定了其它组件进行调用的方法。
> 引自----[百度百科](https://baike.baidu.com/item/OOP)


# 封装的好处
------
## 降低耦合性
&emsp;&emsp;封装后，模块为外部程序提供若干数据访问入口，这些入口是固定，当多模块之间相互协调工作时，某模块内部发生变化，不会影响到其它外部模块。
## 隐藏内部信息
&emsp;&emsp;提供外部访问入口后，模块内部将属性设置为`private`，拒绝外部直接访问，对于不想对外部开放的数据，可去除`get`方法，实现隐藏。
&emsp;&emsp;就好比你的工资卡要放到你老婆那里，日积月累，你不知道工资卡里有多少钱，当你想用钱的时候，要先跟老婆说，然后你老婆从卡里转出钱给你。而同时，为了供孩子上学，你老婆又偷偷存了一个存折，你跟你老婆要存折里的钱，你老婆也不会给你。
&emsp;&emsp;这里存在一个误区，很多人认为程序是人手动写出来的，不管属性设置成`public`、`private`，只要在写代码的时候，不去直接调用就好了。这种想法确实也没毛病，但是大部分情况下，都是团队开发，你不能保证某一天系统进行修改的时候，某个小同事会不会因为着急图省事儿，直接就访问属性去了。

<!-- more -->

## 精确处理内部成员
&emsp;&emsp;这里实际上与** 降低耦合性 **有关联，因为当某些属性涉及业务逻辑时，被多模块重复使用，如果直接由外部模块进行处理，就会增加** 公共耦合 **，下面给出两个例子做对比：

&emsp;&emsp;首先是不进行封装的情形
`Persion.java`:
```java
package com.libs;

public class Persion {
    public int height;
}
```
&emsp;&emsp;可以看到`Persion`类中存在一个属性`height`代表身高，那么，在使用这个类的时候，人类的身高是有限定的：
`Index.java`:
```java
package com;

import com.libs.Persion;

public class Index {
    public static void main(String[] args) {
        Persion p = new Persion();
        // 500厘米
        int height = 500;
        
        if ( height >= 230 ) {
            System.out.println("你以为你是姚明么？");
        } else if ( height <= 20 ) {
            System.out.println("你以为你是异形么？");
        } else {
            p.height = height;
        }
    }
}
```
&emsp;&emsp;从上面的例子，可以看到，我们在主类对`Persion`类属性进行控制，这种简单的测试代码当然看起来无所谓，但是如果说`Persion`类是实际项目中某个重要的实体，被多个其它模块调用，那么，就会出现** 公共耦合 **。接下来，我们对`Persion`类进行封装，来解决这个问题。
`Persion.java`:
```java
package com.libs;

public class Persion {
    private int height;

    public void setHeight(int height) {
        if ( height >= 230 ) {
            System.out.println("你以为你是姚明么？");
        } else if ( height <= 20 ) {
            System.out.println("你以为你是异形么？");
        } else {
            this.height = height;
        }
    }
}
```

`Index.java`:
```java
package com;

import com.libs.Persion;

public class Index {
    public static void main(String[] args) {
        Persion p = new Persion();
        p.setHeight(300);
    }
}
```
&emsp;&emsp;经过封装后，在类内部对属性进行精确控制，外部多个模块调用时，全部走`set`方法，可以避免上面的问题出现。

## 在不影响外部逻辑时，改变内部细节
&emsp;&emsp;在上面的例子中，我们将`Persion`身高属性，设置成了`int`，但是我们发现它缺少单位，当我们想要修改`height`属性的表述方式的时候，如果没有进行封装，会变得很麻烦，如上例中，我们需要在每一个调用位置都修改一遍。进行封装后，只需要在内部修改`set`、`get`方法即可：
`Persion.java`:
```java
package com.libs;

public class Persion {
    // 为身高加上单位 cm
    private String height;

    public void setHeight(int height) {
        if ( height >= 230 ) {
            System.out.println("你以为你是姚明么？");
        } else if ( height <= 20 ) {
            System.out.println("你以为你是异形么？");
        } else {
            this.height = height + "cm";
        }
    }
}
```
# 封装的原则
&emsp;&emsp;封装并不是机械式的把所有属性加上`get`、`set`方法。
## 提供尽可能少的数据出入口
&emsp;&emsp;在封装过程中，要尽可能分析透业务需要，对不必要暴露的访问入口要尽可能屏蔽掉，例如一个指向`人`的实体，我们应该隐藏掉他的家庭信息、经济信息等（这里只是通过对比现实中的情况举例子）。
## 尽可能多的在内部实现细节
&emsp;&emsp;实际上，进行分装后，外部模块在使用时，应该直接拿到可用信息，而不需要在进行二次处理，在输入数据时则应该不进行任何处理，仅提供原始数据。这样一来，不仅仅降低了耦合性，还增加了代码复用的可能。