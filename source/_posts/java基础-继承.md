---
title: java基础-继承
date: '2018/11/14 22:48:53'
categories:
  - 编程语言
  - java
  - 原生
  - 基础
tags:
  - java
  - 基础
abbrlink: 41083d62
---

# 简介
------
&emsp;&emsp;与封装类似，继承也是面向对象编程的特性之一，利用继承可以提升代码复用性，并且可以实现多态特性。
> ** Inheritance and behavioral subtyping **
It is intuitive to assume that inheritance creates a semantic "is a" relationship, and thus to infer that objects instantiated from subclasses can always be safely used instead of those instantiated from the superclass. This intuition is unfortunately false in most OOP languages, in particular in all those that allow mutable objects. Subtype polymorphism as enforced by the type checker in OOP languages (with mutable objects) cannot guarantee behavioral subtyping in any context. Behavioral subtyping is undecidable in general, so it cannot be implemented by a program (compiler). Class or object hierarchies must be carefully designed, considering possible incorrect uses that cannot be detected syntactically. This issue is known as the Liskov substitution principle.
> 引自----[维基百科](https://en.wikipedia.org/wiki/Object-oriented_programming#Inheritance_and_behavioral_subtyping)

<!-- more -->

# 何为继承
&emsp;&emsp;继承是描述两个类的一种关系，** `IS-A` **，比如我们拿出狗和猫两种生物，我们可以说狗是动物，猫是动物，因此我们可以说狗继承了动物，猫继承了动物，但是不能泛泛的说动物是狗、是猫（动物这个分类包含的更加广泛，这种观点，我们可以称之为** 向上转型 **）。
&emsp;&emsp;其中，狗、猫两种生物，具有一些共同的属性和行为：重量、颜色、跑、叫...等等，那我们就可以将这些他们共有的属性和行为抽取出来，放到动物这个父对象当中，然后继承动物这个父对象，获得这些共同的属性。
&emsp;&emsp;然而除了这些共同属性和行为外，各个子类还会具有各自独特细化的属性与行为，比如狗可以摇尾巴，猫咪可以跳高。并且各个子类也可以用自己的方式去实现从父类继承来的行为，比如动物有** 叫 ** 这个行为，但是狗继承来之后，变成了“汪汪叫”，而猫咪继承来之后则变成了“喵喵叫”。
&emsp;&emsp;同时，还存在这样一种情况，动物在进化过程中，有一些特性被深深的埋在基因当中，虽然动物具有这种属性，但是各个实际的物种并不能获得和体现出来。

&emsp;&emsp;** 通过上面的描述，我们可以这样说：狗是动物、猫是动物，因此，狗与猫都继承动物，则动物是狗与猫的父类（或超类），狗与猫是动物的子类（或派生类）。继承后，子类共享了父类的数据，既狗与猫获得了动物的全部属性及方法，这个过程可以称作“泛化”。并且狗与猫各自存在自身特有的属性与方法，同时根据自身性质，重新定义了某些从动物继承来的方法。**

&emsp;&emsp;下面给出具体的实现代码以便后续讲解：
** 目录结构 **
```bash
├── src
│   └── com
│       ├── Index.java
│       └── libs
│           ├── Cat.java
│           ├── Dog.java
│           └── ext
│               └── Animal.java
```
** Animal.java **:
```java
package com.libs.ext;

public class Animal {

    protected String color;
    protected String weight;
    private String unknownDNA;

    public Animal() {
        this.unknownDNA = "一段未知的DNA";
        System.out.println("一只动物诞生了");
    }

    public void bray() {
        System.out.println("吼叫");
    }

    public void run() {
        System.out.println("一只小动物跑过来");
    }

    public void setColor(String color) {
        this.color = color;
    }

    public String getColor() {
        return color;
    }

    public void setWeight(String weight) {
        this.weight = weight;
    }

    public String getWeight() {
        return weight;
    }
}
```
** Cat.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Cat extends Animal {

    public Cat() {
        System.out.println("诞生的是小猫");
        this.setColor("白色");
        this.setWeight("5kg");
    }

    public void bray() {
        System.out.println("小猫喵喵叫");
    }

    public void jump() {
        System.out.println("猫咪在跳高");
    }

}
```
** Dog.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Dog extends Animal {

    public Dog() {
        System.out.println("诞生的是小狗");
        this.setColor("白色");
        this.setWeight("5kg");
    }

    public void bray() {
        System.out.println("小狗汪汪叫");
    }

    public void shake() {
        System.out.println("狗狗在摇尾巴");
    }

}
```
** Index.java **:
```java
package com;

import com.libs.Cat;
import com.libs.Dog;

public class Index {
    public static void main(String[] args) {
        Dog d = new Dog();
        Cat c = new Cat();

        d.bray();
        c.bray();

        d.shake();
        c.jump();

        d.run();
        c.run();
    }
}
```
** 运行结果 **

```bash
一只动物诞生了
诞生的是小狗
一只动物诞生了
诞生的是小猫
小狗汪汪叫
小猫喵喵叫
狗狗在摇尾巴
猫咪在跳高
一只小动物跑过来
一只小动物跑过来
```
&emsp;&emsp;根据上面的例子，我们可以总结出以下几点！

## 子类无法继承父类中private的属性与方法
&emsp;&emsp;如同隐藏在动物DNA中的突变一样，子类继承父类后，若父类中某属性、方法有`private`标志，那么此属性、方法虽然可以传递到子类（猫、狗拥有这段基因），但是子类无法使用。如上面代码所示，父类`Animal`中存在`private`属性`unknownDNA`，子类继承后，在运行阶段调试，在调试信息中可以看到：

```bash
Local variables:
d = instance of com.libs.Dog(id=400)
c = instance of com.libs.Cat(id=401)
main[1] dump d
 d = {
    com.libs.ext.Animal.color: "黑色"
    com.libs.ext.Animal.weight: "7kg"
    com.libs.ext.Animal.unknownDNA: "一段未知的DNA"
}
main[1] dump c
 c = {
    com.libs.ext.Animal.color: "白色"
    com.libs.ext.Animal.weight: "5kg"
    com.libs.ext.Animal.unknownDNA: "一段未知的DNA"
}
```
&emsp;&emsp;此时`d`与`c`两个对象中，存在`unknownDNA`属性，但是如果你在`Dog`与`Cat`类中，利用代码调用`unknownDNA`属性，`System.out.println(this.unknownDNA)`或`System.out.println(super.unknownDNA)`，则会在编译阶段报错`error: unknownDNA has private access in Animal`。因此，子类无法直接使用父类中标识为`private`的属性或方法，但是可以通过继承父类的方法来间接使用此属性，我们来修改一下`Animal`、`Dog`类：

** 在`Animal`类结尾加入以下代码 **
```java
package com.libs.ext;

public class Animal {

    ...
    ...

    public String getUnknownDNA() {
        return this.unknownDNA;
    }
}
```

** 在`Dog`构造器结尾调用从父类继承的`getUnknownDNA`方法并输出到控制台 **
```java
...
    public Dog() {
        ...
        ...
        System.out.println("获取了一段未知DNA：" + this.getUnknownDNA());
    }
...
```

** 执行后，得到以下结果 **
```bash
一只动物诞生了
诞生的是小狗
获取了一段未知DNA：一段未知的DNA
一只动物诞生了
诞生的是小猫
小狗汪汪叫
小猫喵喵叫
狗狗在摇尾巴
猫咪在跳高
一只小动物跑过来
一只小动物跑过来
```
&emsp;&emsp;在这个例子中，我们通过调用从父类继承来的`getUnknownDNA()`方法，来获得`unkownDNA`属性，实际上这里我们在调用时可以直接写`super.getUnknownDNA()`，具体原因将在后续的文章中提到。
&emsp;&emsp;以上是一种使用父类`private`属性或方法的途径，实际上，在开发中，应该尽量将父类的属性进行私有化封装，之后通过`set`、`get`方法进行操作，不知道为什么的，可以回顾一下上一篇中所讲解的封装。
## 子类可重写父类方法
&emsp;&emsp;在上面的例子中，`Animal`类存在一个`bray`方法，代表动物的叫声，而我们定义了原始的动物的叫声，仅仅是吼叫：
```java
public void bray() {
    System.out.println("吼叫");
}
```
&emsp;&emsp;当`Dog`、`Cat`继承了`Animal`后，都把叫声进化成了自己的叫声：
```java
public void bray() {
    System.out.println("小狗汪汪叫");
}

public void bray() {
    System.out.println("小猫喵喵叫");
}
```
&emsp;&emsp;这种处理方式，就叫做** 方法重写（Overriding） **，通过方法重写，我们可以增加类的多样性，更精细的刻画子类功能。并且，这也是多态的一种实现形式。
## 子类可拥有自身独有的方法与属性
&emsp;&emsp;上面的例子中，`Dog`、`Cat`除了继承了`Animal`的属性和方法，它们各自还有自身独特的方法，其中`Dog`可以摇尾巴（`shake`），`Cat`可以跳高（`jump`）。
&emsp;&emsp;所以说，子类实际上是父类扩展与完善，在开发中，不同的子类，通过自身的独特属性和方法，为父类增加了多样性。

# 继承中需要注意的细节
## protected标识
&emsp;&emsp;在我们的例子中，`Animal`类存在两个被`protected`修饰的属性`color`、`weight`，它是告诉编译器，此属性仅可被子类访问，也就是说，当`Dog`、`Cat`继承了`Animal`后，这两个属性在`Dog`、`Cat`所实例化的对象中，就变为了“`private`”，此时，对于实例对象而言，这两个属性是不可访问的，如果在编码阶段在主类`Index`中写了访问这两个属性的代码，那么在编译阶段，将会报错`error: unknownDNA has private access in Animal`。
&emsp;&emsp;那么，我们该如何使用这两个属性呢？首先，在`Dog`、`Cat`两个子类的内部，我们可以通过`this.color`、`this.weight`、`super.color`、`super.weight`来访问这两个属性。如果想在子类外使用（例如在主类`Index`中），我们不能直接通过实例对象调用这些属性，但是我们看到存在四个被`public`修饰的方法`setColor`、`getWeight`、`setWeight`、`getWeight`，我们可以通过这四个方法来读写这些属性（这相当于子类继承父类的封装）。

## 构造函数
&emsp;&emsp;构造函数又称为** 构造器 **，其作用实例化一个对象，并且可在实例化过程中初始化一些信息。程序运行阶段，`new`一个对象产生新实例的时候，此对象的构造函数将被自动调用。
&emsp;&emsp;与属性和方法不同，子类在继承父类后，不会继承父类的构造器，但是在实例化一个子类的时候，父类的构造器会被自动调用，我们回过头来继续看之前示例的输出结果：
```bash
一只动物诞生了
诞生的是小狗
...
...
一只动物诞生了
诞生的是小猫
...
...
```
&emsp;&emsp;在主类`Index`中，我们的调用顺序是`Dog d = new Dog();`、`Cat c = new Cat();`，因此两个`d`、`c`两个实例的产生顺序是正确的，但是在每个实例产生之前，都输出了`一只动物诞生了`，这是我们在`Animal`构造器中输出的，因此，父类的构造器被自动的调用了。那么它是在什么时候、什么位置被调用的呢？我们稍微修改一下`Dog.java`，在他的构造方法体的首末行增加`super();`来手动调用父类构造器！

** Dog.java **
```java
package com.libs;

import com.libs.ext.Animal;

public class Dog extends Animal {

    public Dog() {
        super();

        System.out.println("诞生的是小狗");
        this.setColor("白色");
        this.setWeight("5kg");
        System.out.println("获取了一段未知DNA：" + this.getUnknownDNA());

        super();
    }

    public void bray() {
        System.out.println("小狗汪汪叫");
    }

    public void shake() {
        System.out.println("狗狗在摇尾巴");
    }

}
```
&emsp;&emsp;修改完成后，编译运行，但是会在编译阶段得到一个错误`error: call to super must be first statement in constructor`，意思是说在调用父类构造方法时必须在当前子类构造方法的第一行，很显然，我们在最后一行增加的`super();`产生了这个错误，我们将最后一行的`super();`删除，重新编译运行，得到结果：
```bash
一只动物诞生了
诞生的是小狗
...
...
一只动物诞生了
诞生的是小猫
...
...
```
&emsp;&emsp;很显然，得到的结果与我们没增加`super();`时相同，也就是说如果我们不显示的调用父类构造方法，那么编译器在编译时，会自动将父类构造方法放在子类构造方法的第一行，并且自动执行。
&emsp;&emsp;我们的例子中，无论是`Animal`还是它的子类`Dog`、`Cat`，都只存在无参构造方法，但是这里有一种情况，虽然编译器会在子类实例化时，自动调用父类构造方法，但是只能调用默认的无参数构造方法，下面我们简化一下我们上面的例子，来演示有关父类构造方法的调用！

** Animal.java **:
```java
package com.libs.ext;

public class Animal {

    protected String color;
    protected String weight;
    private String unknownDNA;

    public Animal() {
        System.out.println("默认调用了父类的无参构造方法");
    }

    public Animal(String a) {
        System.out.println("显示调用了父类的有参构造方法，传入参数：" + a);
    }
}
```
** Cat.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Cat extends Animal {
    public Cat() {
        super("猫");
    }
}
```
** Dog.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Dog extends Animal {
    public Dog() {}
}
```
** Index.java **:
```java
package com;

import com.libs.Cat;
import com.libs.Dog;

public class Index {
    public static void main(String[] args) {
        Dog d = new Dog();
        Cat c = new Cat();
    }
}
```
** 输出结果 **
```bash
默认调用了父类的无参构造方法
显示调用了父类的有参构造方法，传入参数：猫
```
&emsp;&emsp;通过例子，我们看到，在`Dog`类中默认调用了父类的无参构造方法，在`Cat`类中我们显示的调用了父类的有参构造方法。实际上如果删除掉`Animal`中的无参构造方法，在实例化`Dog`时，会产生错误：
```bash
./com/libs/Dog.java:7: error: constructor Animal in class Animal cannot be applied to given types;
    public Dog() {
                 ^
  required: String
  found: no arguments
  reason: actual and formal argument lists differ in length
1 error
```
&emsp;&emsp;通过上述说明，我们总结以下几点：
- 在实例化子类时，父类的构造器会被自动调用
- 父类的构造器需在子类构造器的第一行执行
- 若父类存在多个构造器，子类会默认调用父类的无参构造器
- 若父类不存在无参构造器，则子类必须显示的调用父类的有参构造器

## 向上转型
&emsp;&emsp;在解释什么是继承时，我们说过* “我们可以说狗是动物，猫是动物，因此我们可以说狗继承了动物，猫继承了动物，但是不能泛泛的说动物是狗、是猫（动物这个分类包含的更加广泛，这种观点，我们可以称之为** 向上转型 **）” *。
&emsp;&emsp;子类继承父类时，我们可以理解为一个泛化过程，`Animal`包括更广泛的集合，而`Dog`、`Cat`都是这广泛集合中的一种，那么我们就可以说，`动物-狗`、`动物-猫`，我们实际上把狗、猫看作了动物（子类看作父类）。类似的也可将人民币、美元看作货币，大米、小米看作谷物等等。
&emsp;&emsp;下面用代码给出** 向上转型 **的实例！

** Animal.java **:
```java
package com.libs.ext;

public class Animal {

    public void eat() {
        System.out.println("Animal 在吃东西");
    }
}
```
** Cat.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Cat extends Animal {
    public void jump() {
        System.out.println("cat 在跳");
    }
}
```
** Dog.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Dog extends Animal {}
```
** Index.java **:
```java
package com;

import com.libs.Cat;
import com.libs.Dog;
import com.libs.ext.Animal;

public class Index {

    public static void doEat(Animal animal) {
        animal.eat();
    }

    public static void doJump(Animal animal) {
        animal.jump();
    }

    public static void main(String[] args) {
        Dog d = new Dog();
        Cat c = new Cat();

        Index.doEat(d);
        Index.doEat(c);

        Index.doJump(c);
    }
}
```

&emsp;&emsp;当我们试图编译上述代码是，会得到如下错误：

```bash
com/Index.java:14: error: cannot find symbol
        animal.jump();
              ^
  symbol:   method jump()
  location: variable animal of type Animal
1 error
```
&emsp;&emsp;我们本以为调用`Index.doJump(c);`时，传入的是`Cat`的实例c，而`Cat`的实例对象中是存在`jump`方法的，但是在`doJump`的形参中，我们指定了参数类型是`Animal`父类，虽然`Dog d`、`Cat c`可以向上转型为`Animal`，但是这始终是一种** 专用类型至通用类型的转换 **，这是将会丢失原专用类型的独有的方法。
&emsp;&emsp;此时，即便是我们将`Index.doJump(c);`这段代码删掉，编译时程序依然报错，因为`doJump`的形参`Animal`对象是不存在`jump`方法的，我们删除`Index.doJump(c);`与`doJump`方法后编译代码并运行得到结果：
```bash
Animal 在吃东西
Animal 在吃东西
```
&emsp;&emsp;可以看到，虽然传入的实参是`Dog d`、`Cat c`，但是其已经向上转型为`Animal`。
# 继承中的内存关系
&emsp;&emsp;下面做一些简化，我们重新定义`Animal`、`Dog`、`Cat`这三个类，并画出相应的内存关系图！
** Animal.java **:
```java
package com.libs.ext;

public class Animal {

    protected String weight;

    public void setWeight(String weight) {
        this.weight = weight;
    }
}
```
** Cat.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Cat extends Animal { 

    public Cat() {
        this.setWeight("5kg");
    }
}
```
** Dog.java **:
```java
package com.libs;

import com.libs.ext.Animal;

public class Dog extends Animal {

    private String color;

    public Dog() {
        this.setWeight("5kg");
        this.setColor("black");
    }

    public void setColor(String color) {
        this.color = color;
    }
}
```
** Index.java **:

```java
package com;

import com.libs.Cat;
import com.libs.Dog;

public class Index {
    public static void main(String[] args) {
        Dog d = new Dog();
        Cat c = new Cat();
    }
}
```
&emsp;&emsp;上述代码运行完后，`Animal`、`Dog`、`Cat`的内存关系，大致如下图：
![图片无法显示](/images/java基础-继承/stack.png)

&emsp;&emsp;首先，当代码执行到`Dog d = new Dog();`时，由于父类的构造方法会先行执行，因此`Dog`的父类`Animal`将会优先实例化完成，jvm会在堆中开辟一块内存，存放`Animal`实例，`Animal`实例中的`setWeight`方法指向方法区的对应的`public void setWeight(String weight)`的地址。紧接着，`Dog`实例化完成，其中`super`代表`Dog`的超类，因此指向`Dog`的`Animal`实例地址，`Dog`实例中的`setColor`方法指向方法区对应的`public void setColor(String color)`的地址。与此同时`Dog`构造方法执行完成后，运行了`setWeight`、`setColor`，这是将会在字符常量池存放两个字符常量`black`、`5kg`，`Dog`实例中的`color`属性会指向字符常量池中`black`的地址，`Animal`实例中的`weight`属性会指向字符常量池中`5kg`的地址，最后，jvm在栈中开辟一小块内存，存放变量`Dog d`所指向的`Dog`实例地址。
&emsp;&emsp;类似的，`Cat c = new Cat();`执行完后，也会在栈中存在一个`Cat c`指向`Cat`实例的地址。
&emsp;&emsp;这里有一点需要特别说明的，就是`Dog`构造方法执行完成后运行`setWeight`、`setColor`的过程，以`setWeight`详细举例：
&emsp;&emsp;** 首先，jvm会在`Dog`实例的内存中查找，看是否能找到`this.setWeight`（即我们示例代码里面的代码表述，代表`Dog`自身的`setWeight`方法），这里很显然找不到，因为`Dog`并没有重写父类的`setWeight`方法，找不到，然后去`super`里找（也就是执行`super.setWeight`），前面说过了，`super`指向`Animal`实例，因此找到了，接下来去方法区去除方法体执行这个方法（实际上方法区里面存的已经不是我们写的代码，而是编译完的一些东西，具体的内容如果有精力的话会在后续文章中讲解）。 **
&emsp;&emsp;同理，执行`setColor`也是这个步骤，不同的是在执行`this.setColor`时，找到了就直接执行了。

# 继承的缺陷
&emsp;&emsp;以上全文说明了继承的作用与一些需要注意的细节，但是它也存在一些缺陷，我们在软件开发中，应该结合继承的优点和缺陷，谨慎选择是否需要使用，不能仅仅是“为了继承而继承”。


1. ** 耦合性太强 **
&emsp;&emsp;存在继承关系子类与父类属于强耦合，内部数据互通，并且若业务逻辑改变造成父类的变更，则继承它的诸多子类均随之改变。
2. ** 破坏了封装 **
&emsp;&emsp;对于子类而言，父类的一切都是透明的。

# 何时继承
&emsp;&emsp;在开发过程中，详细的分析一下，子类是否需要向父类进行向上转型。如果需要这么做，那么继承是必须的，如果不需要，那么就要慎重考虑是否真的需要继承。
