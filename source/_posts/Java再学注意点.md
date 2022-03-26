---
title: Java再学注意点
date: 2021-02-27 14:44:30
updated: 2021-03-08 16:32:00
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/X9DhgVGeLqSa7Wx.jpg
tags:
- java
categories:
- 编程语言
---


# Java再学注意点（持续更新）

> 为什么要说“再学”？只因本人从大一上开始就断断续续的学习了java基础。但是因为一直没有在实战中使用（都知道代码不敲绝对会忘≡(▔﹏▔)≡），于是在此时决定系统性的认真学习一下。此笔记就成为了再学习的注意点总结<font color=brown>（注意这个笔记并不是Java教学笔记）</font>。（此笔记的参考主要来自和C系列语言的比较）

## 总体来说

Java是95年诞生的，同时诞生的还有前端兄弟Javascript（*尽管是脚本语言*），python就相对早一点，诞生于91年。总体来说Java感觉上就是C系列语言的翻版（*毕竟就是静态语言，而且我就只学过C系列*），**很多的东西和C是互通的**。但感觉上有一点麻烦的是Java无时无刻不在体现它的**面对对象的思想**，以至于我发现一个简单的函数功能**都必须在一个类中实现**，感觉上是比C更为严格了（*不过规范毕竟还是有规范的好处*）。

# main方法

和C的“main函数”不同的是，Java直接将main这个程序入口函数**完全整成了一个类的方法**，这个类在一个项目（Project）中通常是叫做App.class（当然我们初学时可以随便起名，随便创建单文件程序）。

*不得不说，从一个普通函数变成一个类的方法，Java中的main入口确实规范且生硬。于是乎就会出现这样的Java程序经典开头：*

```java
public class App {
    public static void main(String[] args) {
        
    }
}
```

其中要是加一句`System.out.println('Hello World!');`就是一个经典输出`Hello World`程序了。

## javac和java

我们随便写的一个`.java`文件要想编译运行的话就得用上标题上的命令：`javac`和`java`。通过`javac`将`.java`文件编译成`.class`文件，然后`java`一波run就运行了这个程序。

## main入口参数

在学C的时候我们都知道main函数实际上是可以传递参数的（通过终端命令行），同样Java也可以这样，而且是十分显式的（`String[] args`)。这预示着我们可以传递字符串数组并进行相应的操作。

# IDEA的部分快捷键

- `main`

  直接补全main方法结构

- `sout`

  补全`System.out.println();`

- <kbd>Ctrl</kbd>+<kbd>w</kbd>

  选中word或整行

- `new ClassName`+<kbd>Alt</kbd>+<kbd>Enter</kbd>

  直接补全整个创建对象实例

- <kbd>Alt</kbd>+<kbd>Insert</kbd>

  呼出创建新文件的窗口

- <kbd>Alt</kbd>+<kbd>1</kbd>

  调出或关闭Project目录

- <kbd>Alt</kbd>+<kbd>4</kbd>

  调出或关闭run窗口

# Java中的数组

> 很多人（包括我）学过C之后对Java的数组创建形式不太习惯，于是老是搞混（其实两种在Java中都是对的），还是细说一下比较好。

```java
// 可以这样直接声明时赋值
// 这样同样有两种方式
int[] n1 = {12, 34, 56};	// 官方推荐写法
int n2[] = {12, 34, 56};	// C语法写法
/* 同时下面这一种IDEA还会警告C-style array declaration of 
local variable 'n2' */
```

当然我们还可以先声明：

```java
int[] n = new int[10];
// 这里写法类似创建对象实例
```

*不过这里我们依然还是要指明数组中的元素个数，也就是说无论用以上哪种写法，我们都必须要将数组的分配空间确定下来（或许之后或有一种方法来完成类似C中的动态分配？）*

# Java中的'函数'

> 总所周知，Java是没有函数这个说法的，于是就造成了很多的麻烦。比如说在C和Python中可以自定义函数，于是可以随意调用函数实现简单程序的封装。不过，Java还是可以使用‘函数’的思想的。

如果我们在一个类上写了一个静态(static)的方法(*当然前提是在一个模块中*)，然后我们就可以在这个文件中调用方法，此时完成了类似C或者Python中函数的作用。

```java
// 这样调用
Class.method(args);		// 当然得是静态方法
```

*你可以理解成这个静态方法是类方法，而不是对象的方法。普通的类方法是需要实例化出一个对象然后通过对象来调用。*

当然，既然是类的方法，我在类里面直接调用绝对是可行的：

```java
// 在同一个类中，方法调方法
public static void a(){...}
public static void b(){
    a();	// 真 - 直接调
}

// 同时在一个类中，非静态也可‘真直调’
public void a(){...}
public void b(){
    a();
}

// 但这种是错的！
public void a(){...}
public static void b(){
    a();		// 这个是调不了的
}
// 因为静态方法的时间周期是和类一样的，但是非静态会迟一些
// 所以调不出来
```

# 继承（extends）

既然是面向对象，肯定是少不了封装，继承，多态。
然而Java中的继承和C++并不相同，Java中类只能单继承，而C++是可以多重继承的。**However，接口是可以多继承的。**

## 子类的构造器

当创建一个子类的对象实例时，会自动调用子类相应的构造函数，同时也会自动调用父类的**无参构造函数**（注意，顺序是先父类再子类）。

如果在这种情况下，你想要手动调用父类的有参构造，就必须将这条代码写到子类构造函数的第一条。

***如果定义了有参构造函数，最好是在父类中写上无参构造（或者每个类定义一个无参构造***

## super

1. `super`调用父类的构造方法，必须在构造方法的第一个。
2. `super`必须只能出现在子类的方法或者构造方法中。
3. `super`和`this`不能同时调用构造方法！

## 方法重写

当子类继承了父类，子类就可以调用父类的方法了，当然子类还可以重写（override）父类的方法。

***此处需要注意的是当要重写的方法是静态（static）方法时，此时并不是重写，而是在子类中声明了一个类方法（静态方法本来就是类方法）。***

```java
// package com.oop.a;
public class A extends B{
    @Override   // 这里的注解有功能性
    public void test() {...}
}

// package com.oop.b;
public class B{
    public void test() {...}
}

// import com.oop.b.B;

public class App {
    public static void main(String[] args) {
        A a = new A();
        a.test(); 	// 调用的A
        
        B b = new A();
        b.test();	// 调用的B
    }
}
```

## java的多态

> 其实java的多态和C的也没什么区别，也是通过继承的关系，子类来重写父类的反法。在声明一个子类对象之后，不同的实例就有不同响应的反法。

```java
public class Person {
    public void run(){
        System.out.println("father");
    }
}
public class Student extends Person{
    @Override
    public void run(){
        System.out.println("son");
    }

    public void eat(){
        System.out.println("eat");
    }
}

// Main
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello World!");

        Student s1 = new Student();
        Person s2 = new Student();

        s2.run();
        s1.eat();
        s2.eat();
    }
}
```

这里的`s2`就是使用了父类型，可以指向子类，但是不能调用子类独有的方法。（*同时还是可以调用子类的重写方法的*）。但是`s2`调用了`eat`方法就是错的。

**ps：对象能执行哪些方法，主要看对象左边的类型，和右边关系不大**

如果我们硬是要使用`s2`来调用`eat()`方法，我们可以使用类的强制转换：

```java
((Student) s2).eat()
```

总的来说，多态的经典格式就是：`Father f1 = new Son();`

*ps：再次强调一下，`static`方法，属于类，它不属于对象实例*

**同时还记住一个口诀：**
- 子转父：向上转型，直接转，丢失子类中原本可直接调用的特有的方法；
- 父转子：向下转型，强制转，丢失父类被子类所重写调的方法。

## static详解

> 首先记住一个顺序：静态 > 匿名 > 构造

```java
public class Person{
    {
        System.out.println("匿名代码块");
    }

    static {
        System.out.println("静态代码块");
    }

    public Person(){
        System.out.println("构造方法");
    }

    public static void main(String[] args){
        Person person = new Person();
    }
}
```

*很神奇的是我们的一个对象被生成时会调用这三种代码块。*
