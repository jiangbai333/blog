---
title: java基础-实例抽象化
date: '2018/11/17 21:14:53'
categories:
  - 编程语言
  - java
  - 原生
  - 基础
tags:
  - java
  - 基础
abbrlink: f2d7f0de
---

# 简介
------
&emsp;&emsp;前面的文章中，我们说了继承，表示一种实例的泛化过程，但是在父类当中存在的都是具体的方法（即行为），实际开发中，大部分情况下，我们仅仅需要抽象出子类的行为，具体实现由子类来完成。这篇文章里，我们通过说明`抽象类`和`接口`，并结合例子，来实现java中如何对对象进行抽象化。

&emsp;&emsp;实现抽象化有两种方式一种是利用`接口`：
> 接口被用来统一类的共通行为，当不同的类需要进行信息共享时，是不需要特别去创建类间的关系。举例来说，一个人（Human）及一只鹦鹉（Parrot）都会吹口哨（whistle），然而Human及Parrot不应该为Whistler的子类，最好的做法是令他们为Animal的子类，而他们可以使用Whistler的接口进行沟通。
> 引自----[维基百科](https://zh.wikipedia.org/wiki/%E6%8E%A5%E5%8F%A3)

&emsp;&emsp;另一种是利用`抽象类`：
> 抽象类往往用来表征对问题领域进行分析、设计中得出的抽象概念，是对一系列看上去不同，但是本质上相同的具体概念的抽象。
> 引自----[百度百科](https://baike.baidu.com/item/%E6%8A%BD%E8%B1%A1%E7%B1%BB/4748292#3_3)

<!-- more -->

# 示例代码
&emsp;&emsp;我们的示例代码中共存在四个类，一个接口，项目的目录结构如下：
```bash
├── src
│   └── com
│       ├── Index.java
│       └── libs
│           ├── ast
│           │   └── Weapon.java
│           ├── Grenade.java
│           ├── inf                                                            
│           │   └── Explosion.java
│           └── Spear.java
```

** Weapon.java 武器抽象类 **
```java
package com.libs.ast;

public abstract class Weapon {

    private int sequence;

    public Weapon(int sequence) {
        this.sequence = sequence;
    }

    public int getSequence() {
        return this.sequence;
    }

    public abstract void fighting();
}
```

** Explosion.java 描述爆炸的接口 **
```java
package com.libs.inf;

public interface Explosion {

    public static final String description = "爆炸会产生响声，并且伤害周围的人";

    public void explosion();
}
```

** Spear.java 武器长矛 **
```java
package com.libs;

import java.util.Random;

import com.libs.ast.Weapon;

public class Spear extends Weapon {

    public Spear() {
        super(new Random().nextInt(1000) + 1);
    }

    public void fighting() {
        System.out.println("挥舞着 " + this.getSequence() + " 号长矛向敌人刺去");
    }
}
```

** Grenade.java 武器手雷 **
```java
package com.libs;

import java.util.Random;

import com.libs.ast.Weapon;
import com.libs.inf.Explosion;

public class Grenade extends Weapon implements Explosion {

    public Grenade() {
        super(new Random().nextInt(1000) + 1);
    }

    public void fighting() {
        System.out.println("投掷 " + this.getSequence() + " 号手雷到敌人阵地");
        this.explosion();
    }

    public void explosion() {
        System.out.println(this.getSequence() + " 号手雷爆炸了");
        System.out.println(Grenade.description);   
    }
}
```

** Index.java 主类 **
```java
package com;

import com.libs.Grenade;
import com.libs.Spear;

public class Index {

    public static void main(String[] args) {

        Spear s = new Spear();
        Grenade g = new Grenade();

        s.fighting();
        g.fighting();
    }
}
```

** 运行结果 **
```bash
挥舞着 456 号长矛向敌人刺去
投掷 12 号手雷到敌人阵地
12 号手雷爆炸了
爆炸会产生响声，并且伤害周围的人
```

# 接口与抽象类
&emsp;&emsp;在java中，抽象类与接口是实现对实例对象进行抽象的两种机制，但是它们对事物的抽象理念是不同的，接口往往是脱离对象的本质对某种行为进行抽象，而抽象类则是对具体事物的实例进行抽象。
## 抽象的理念
### 代码层面上
#### 接口
&emsp;&emsp;接口更像是提供了一套行为的模板，实现它的子类会去完成其中的细节。接口中可以声明一个或多个行为的名称，但是不能对它进行实现，也可以提供一个静态常量，用以描述一些事情。如例子中：
```java
public interface Explosion {

    public static final String description = "爆炸会产生响声，并且伤害周围的人";

    public void explosion();
}
```
&emsp;&emsp;它只是告诉即将实现它的子类，“我要描述一个行为，这个行为是爆炸”，“爆炸会产生响声，并且伤害周围的人”，而如何爆炸、什么时候爆炸、爆炸的威力是多大，这些，完全根据实现它的子类来定。并且，如果有任何子类实现了它，那么该类必须实现`explosion`这个行为的细节。
&emsp;&emsp;由于接口中定义的方法，必须被子类实现，因此，接口中的方法必须被显示的标注为`public`，接口本身也必须被`public`所标注。同时接口是可以继承其它接口的，例如我们可以再定义一个接口，描述** 闪光 **这种行为，然后利用`Explosion`继承这个接口（因为爆炸可能会产生闪光），在`Explosion`继承了`Flare`之后，实现`Explosion`接口的类，必须同时实现`public void flare();`方法。
```java
public interface Flare {
    public void flare();
}

public interface Explosion extends Flare {

    public static final String description = "爆炸会产生响声，并且伤害周围的人";

    public void explosion();
}
```
&emsp;&emsp;与继承时父类与子类的关系类似的是，实现接口后，实例存在向上转型的可能，如上述代码中的`Flare`接口，最终会被`Grenade`实现，那么我们可以通过`Flare fg = new Grenade();`来构建一个`Flare`类型的变量，但是它的引用地址则指向内存中的`Grenade`实例，在这里的向上转型的原则与继承中完全一致，因此`fg`只能调用`Flare`接口中被`Grenade`所实现的方法，但是`Flare`中的所有方法必定都会被`Grenade`所实现（因为我们既然能new Grenade，那么证明它不是一个接口也不是一个抽象类，所以它必须实现全部抽象方法）。
&emsp;&emsp;由于接口内没有属性（即类的成员变量）和实际可执行的方法，仅仅存在方法名，因此，单独的接口是不可被`new`关键字作用的，即不可实例化，因此在代码层面上来说，没有被实现的接口就没有存在价值。

#### 抽象类
&emsp;&emsp;抽象类中，不光存在接口中那种没有方法实体的抽象行为，同时还可存在实际的方法以及属性，子类在继承它以后，除了需要重写其中的抽象方法以外，其它的特性与继承完全相同。
&emsp;&emsp;值得注意的是，** 抽象类中可以没有抽象方法 **！你可以亲自修改`Weapon`类试一下：
```java
public abstract class Weapon {

    private int sequence;

    public Weapon(int sequence) {
        this.sequence = sequence;
    }

    public int getSequence() {
        return this.sequence;
    }

    public void fighting() {
        ;
    }
}
```
&emsp;&emsp;修改完成后，编译运行是都可以通过的。这个情况，实际上很有用，比如当** 某些类一定要被继承才能使用 **，因为即使没有抽象方法，该类依然被`abstract`关键字标注，那么在编译阶段就不允许该类被`new`关键字直接作用，也就是说，他不能独自实例化（抽象类只能作为父类），必须依赖子类调用`super()`进行实例化。
&emsp;&emsp;实际上，除了不能直接实例化这件事之外，抽象类的其它用法与普通类没有差别，因此它也能实现接口，继承其它抽象类。

### 思维层面上
&emsp;&emsp;在思维层面上，接口是可以脱离实现类存在的，例如我们例子中的`Explosion`接口，它只是描述爆炸这种行为，至于什么实际对象会发生爆炸、什么时候会发生爆炸，我们不需要知道，也没必要知道。不管有没有子类去实现它，这种行为是存在的，这个接口是存在的。

&emsp;&emsp;但是对于抽象类就不是，因为他既然作为一个父类，那么最少要有两个子类才能抽象出它吧，假如我们的例子中，如果只有长矛一种武器，很显然直接抽象出一个武器父类是很草率的。

## 总结

&emsp;&emsp;通过上面的说明，我们分为接口和抽象类总结出以下几点！
### 接口
- ** 接口内部全部的方法必须声明为`public` **
- ** 接口内部不能有任何实现 **
- ** 在实现接口时，若子类为* 非抽象类 * 则必须实现接口中定义的全部方法 **
- ** 接口中可使用 `static final` 修饰一个不可变成员变量（常量），通过`接口名.变量名`或`子类名.变量名`引用 **
- ** 不可被`new`关键字直接作用，生成实例 **
- ** 实现类可向上转型 **

### 抽象类
- ** 抽象类内部全部的抽象方法必须声明为`public` **
- ** 不管是否存在抽象方法，只要被`abstract`声明后，就会成为抽象类 **
- ** 若抽象类实现一个接口，可实现其中的任意数量的抽象方法，若有未实现的方法，无论是否以抽象方法的形式写入此抽象类，其子类必须实现这些未实现的方法 **
- ** 若抽象类继承另一抽象类，可实现其中的任意数量的抽象方法，若有未实现的方法，无论是否以抽象方法的形式写入此抽象类，其子类必须实现这些未实现的方法。若此抽象类存在自身的抽象方法，不可与父类抽象方法重名，但可重载 **
- ** 不可被`new`关键字直接作用，生成实例 **
- ** 被子类继承后，子类可向上转型 **

# 实际开发中的抽象行为
&emsp;&emsp;上面的例子中，我们用`Explosion`接口继承了`Flare`,最终`Grenade`实现了`Explosion`接口，这样做到底好不好呢？很明显不好，因为我们无形中扩充了`Explosion`接口的行为。
&emsp;&emsp;我们例子中的实例都是武器，其中`Grenade`手雷在爆炸时确实能产生闪光（`Flare`），但是是不是爆炸都能产生闪光呢，很显然不是，比如锅炉因为压力过大而爆炸，气球因为吹的太鼓而爆炸，这些都不存在闪光，对于我们而言，爆炸就是爆炸，闪光就是闪光，但是我们的例子中，把它们结合到了一起，这种思路很显然是错误的。更好的方式是利用`Grenade`去实现`Explosion`、`Flare`两个接口：`public class Grenade extends Weapon implements Explosion, Flare {...}`，代表`Grenade`存在这两个行为，这更符合[ISP原则](https://zh.wikipedia.org/wiki/%E6%8E%A5%E5%8F%A3%E9%9A%94%E7%A6%BB%E5%8E%9F%E5%88%99)。

&emsp;&emsp;另外，在实际开发中，我们应该通过分析，结合抽象类和接口的性质完成对实例的抽象。为了方便描述，我们以电梯为例再来举一个简单的例子。

&emsp;&emsp;抛开属性不谈，电梯可以存在两种行为，`上行`、`下行`，因此我们可以有两种方式去抽象它：
** 接口 **
```java
public interface Elevator {
    public void up();
    public void down();
} 
```
** 抽象类 **
```java
public abstract class Elevator {
    public void up();
    public void down();
} 
```

&emsp;&emsp;目前，这两种抽象方式看起来都没什么问题，但是如果我们为直梯稍微加一点点功能呢？比如说我们为直梯加上观光功能（抽象方法`public void view();`）,这时无论把`view`加到接口中还是抽象类中，都不好。如果加到接口中，那么，观光这个行为，就变成了冗余的，比如说如果在后续的阶段，需要一个观光电车的实例，那么你如何定义？还要重新的去定义这个观光的抽象方法，这严重违反开发中的ISP原则。文章的前面已经提到了，接口是脱离实例对象而存在的，它仅仅描述行为，因此这里我们定将接口定义为`Elevator`本身就存在问题，它太有针对性了，更好的定义应该是`Direction`（运行方向）。
&emsp;&emsp;同时，对于抽象类来说，`上行`、`下行`两个行为，本身就是电梯运行的两种方式，因此它拥有这两个方法是没问题的，但是`public void view();`方法，却不适合它，因为不是所有的电梯都有观光功能，`public void view();`只是某些特殊电梯具有的一种行为。因此以下两种方式是更好的抽象化定义：

** 当你认为电梯存在时 **
&emsp;&emsp;这种情况是指，你肯定了电梯这个对象，它包含** 滚梯 **、** 直梯 **、 ** 货梯 **、** 观光梯 **，你从他们当中，抽象出了电梯的共有行为`上行`、`下行`，并为某些电梯增加了能够观光这种行为。简单说就是，你认为** 我抽象了电梯，但是有些电梯是`SightseeingLadder`，它们具有`view`这种行为，它们能观光 **。
```java
public interface View {
    public void view();
} 

public abstract class Elevator {
    public void up();
    public void down();
} 

//观光梯
public class SightseeingLadder extends Elevator implements View {
    //实现方法
}
```

** 当你觉得电梯不存在时 **
&emsp;&emsp;与上面的情形不同，在这种情况下，你不认为或不知道电梯存在，但是你知道有某种东西或事物，它们可以`上行`、`下行`、`观光`。简单说就是，你认为** 我知道三种行为：`上行`、`下行`、`观光`，这几种行为可能出现到很多实例当中。但是我知道有一种事物，拥有这三种行为，那我们把它叫做`SightseeingLadder`吧 **。
```java
public interface View {
    public void view();
} 

public interface Direction {
    public void up();
    public void down();
} 

//观光梯
public class SightseeingLadder implements Direction, View  {
    //实现方法
} 
```