---
title: Chisel Lang
date: 2023-10-01 17:05:23
updated: 2024-03-04 19:39:00
index_img: https://s2.loli.net/2023/10/01/FXxACeGSV4mIw3L.png
author: Rogers Kelamen
tags:
- HDL
categories:
- 系统底层
---

# Scala Basic Grammar

## function/method

```scala
// first kind
def times2(x: Int): Int = 2 * x
// second kind
def distance(x: Int, y: Int, returnPositive: Boolean): Int = {
    val xy = x * y
    if (returnPositive) xy.abs else -xy.abs
}
```

需要注意的是以上两种都是有返回值(***最后一行***)的，函数参数之后需要加上`=`；而如果没有返回值，就不需要`=`符号

### anonymous function

在scala中，也存在类似于js中的匿名函数(箭头表达式)：

```scala
val intList = List(1, 2, 3)
val stringList = intList.map { i =>
  i.toString
}
```

一般来说，就像其他语言里的匿名函数一样，匿名函数作为函数参数，可以快速填充一个函数的参数

这里的`map`就需要一个函数参数来改变List中的每一个元素，返回新的List

如果想要简单一点，可以传入`_`作为单参的表示：

```scala
val stringList = intList.map(_.toString)
```

## for循环

主要有两种格式:

```scala
// 如果遍历列表数据，很简单
val dogBreeds = List("Doberman", "Great Dane", "Dachshund")
for (breed <- dogBreeds)
    println(breed)

// 可以使用for生成器完成特定次数的for循环
for (i <- 1 to 10)
// 如果是until则不会到最后元素
for (i <- 0 until n)
// 还可以指定循环的步长(by)
for (i <- 0 until n by 2)
```

### 细化粒度

我们还可以加入`if`表达式来进一步帅选出我们希望保留的元素，这些表达式也被称为保护式(guard) e.g.

```scala
for (breed <- dogBreeds
    if breed.contains("Dane")
)println(breed)
// 只会打印出"Great Dane"
```

## 函数式编程数据结构

### Tuple

元组(Tuple)在很多高级语言中都支持，可以解决多个返回值等编程痛点，scala也很方便地支持元组，可以通过TupleN类来构建元组，这里N从1到22(所以最大支持22个元素)

```scala
// 构造元组的集中方法
val t = ("hello", 1, 2.3)
val (t1, t2, t3) = ("world", '!', 0x22)
val (t4, t5, t6) = Tuple3("world", '!', 0x22)
// 通过t._n来访问元组中的第n个元素
println("print the first element: " + t._1) // !! index start by 1 !!
```

#### Pair

一个两元素的元组，因为经常用到，所以被特称为pair。除了可以用小括号构造一个pair以外，scala还提供“箭头操作符”的语法糖：

```scala
// 三者等价
(1, "one")
1 -> "one"
Tuple2(1, "one")
```

### List

列表(List)也是一种序列，是函数式编程中最常用的数据结构

使用`::`想队列头部追加数据，从而创建新的列表:

```scala
val list1 = List("Programming", "Scala")
val list2 = "People" :: "should" :: "read" :: list1
// ++ also ok
val list3 = list1 ++ list2
```

*需要注意的是，Seq的构造方法是`+:`而不是`::`*

### Seq

Seq(代表Sequence)是集合的一种抽象，可以<u>依照固定的顺序</u>便利这类结合抽象。

值得注意的是，Seq是一个参数化类型，就好像Java中的泛型类型。Seq代表着“某类食物的序列”，如果是Java的化参数类型就用`<...>`包裹，但是Scala用的是`[...]`

**Seq只是一个特征(trait)，而不是具体的类，所以创建出的Seq具体对象时，Scala会将它转化为List等有具体类的序列数据类型**

### Range

Range是最好构造的，普通的`1 to 10`就可以构造一个[1:10]的range

## 方法

### 多参数列表

scala是支持多个参数列表的：

```scala
def draw(offset: Point = Point(0.0, 0.0))(f: String => Unit): Unit =
    f(s"drawing(offset = $offset), ${this.toString}")
```

这里draw方法就有两个参数列表，第一个传入一个`Point`类的变量，第二个传入一个匿名函数(有一个String类型参数，无返回值)。

调用这个函数可以采用这样的方式：

```scala
s.draw(Point(1.0, 2.0))(str => println(s"Shape: $str"))
```

当然scala也提供这样的语法糖，讲第二个参数列表用大括号表示为代码块：

```scala
s.draw(Point(1.0, 2.0)) { str =>
    println(s"Shape: $str")
}
```

在chisel中的test很多的test方法都是这样多参数的，所以第二个参数可以写成大括号的形式。

## 包管理(package)

Scala沿用了Java包表示命名空间的做法，但是在其他地方更灵活。Scala允许类名和文件名的不一致，包结构不一定要与目录结构一致。

Java使用`*`作为import的通配符，在Scala中则是使用`_`

可以使用`{}`来包含一个包下的多个对象或方法，e.g.

```scala
import chisel3.util.{DeqIO, EnqIo, log2Ceil}
```

Scala会自动加载一些像`println`这样的方法，`println`其实是`scala`包中`Console`对象的一个方法。所以scala会自动帮我们载入`scala.Console.println`

## OOP

- Abstract Class

- Trait

    类似于抽象类，但是可以继承多个:

    ```scala
    class MyClass extends Trait1 with Trait2 with Trait3 ...
    ```

- Object

    使用`object`关键字声明一个单例类，类似于静态类，一般直接引用就好

- Companion Object

    同名的单例类和普通类同时存在，这时候`new`的就是普通类的实例，直接引用就是单例类，这个单例类就叫做<u>伴生类</u>

- Case Class

    `case class`的参数允许外部访问，同时不需要`new`关键字来实例化对象(因为在编译器内部自动生成了一个伴生类)

    ```scala
    class Nail(length: Int) // Regular class
    val nail = new Nail(10) // Requires the `new` keyword
    // println(nail.length) // Illegal! Class constructor parameters are not by default externally visible

    class Screw(val threadSpace: Int) // By using the `val` keyword, threadSpace is now externally visible
    val screw = new Screw(2)          // Requires the `new` keyword
    println(screw.threadSpace)

    case class Staple(isClosed: Boolean) // Case class constructor parameters are, by default, externally visible
    val staple = Staple(false)           // No `new` keyword required
    println(staple.isClosed)
    ```

注意事项：

1. scala里面的构造方法是`apply()`

2. 像是`IO()`, `Input()`, `Bool()`, `UInt()`, `Seq()`这种没有使用`new`关键字的，大概率就是单例类，要么就是`case class`

### 构造函数参数

- 在主构造函数(也就是普通`class`)中的参数，如果不声明为var或者val，则只能当作一个不可变参数，不能当作类的字段，即外部不能访问该变量：

```scala
class Person(name: String, age: Int) {
    var info = name
}
```

- 如果声明类var或者val，就可以当作类的成员变量，外部可访问：

```scala
class Person(name: String, age: Int) {
    var info = name
}

object test {
    def main(args: Array[String]): Unit = {
        val person = new Person("chen", 15)
        println(person.info)
        println(person.name)
        println(person.age)
    }
}
```

## wired sign

- `???`: 这个<u>三重问号符</u>会主动触发一个`NotImplementedError`

# Sbt Project Management

sbt对于项目的目录结构默认是和maven一样的，具体可以参考[官网上的目录结构](https://www.scala-sbt.org/1.x/docs/zh-cn/Directories.html)一栏

除了可以在`build.sbt`上配置sbt外，还可以在`project`目录中添加文件

主要的几个命令:

- `compile`: 编译

- `run`: 编译并运行

- `test`: 测试**所有**测试集

如果想要单独测试某个测试集的话，可以使用`testOnly`:

```sbt
testOnly pkgname.TestName
```

如果想要更改`build.sbt`后sbt自动reload，加上这一句`Global / onChangedBuildSource := ReloadOnSourceChanges`

- 你可以配置你的project：

    ```sbt
    lazy val projectA =
      project
        .in(file("."))
        .settings(...)
    ```

    只需要在`setting`里面配置就好了，当然每个变量的命名是以小驼峰(camelCase)方式。不过编译和测试是大写字母开头：

    ```sbt
    Compile / sourceDirectory := sourceDirectory.value / "prod",
    Test / sourceDirectory := sourceDirectory.value / "unit",
    ```

# Chisel Extra Grammar

## translate into verilog code

- 当你在bootcamp中

    当我们创建了一个chisel模块之后，我们总可以使用`getVerilog()`来得到等价的verilog:

    ```scala
    println(getVerilog(new MyModule))
    ```

    当然你也可以使用`getFirrtl()`来得到FIRRTL代码(*一种更为简单仅合成的verilog子集代码*):


    ```scala
    println(getFirrtl(new MyModule))
    ```

- 当你在正常开发流程中

    ```scala
    import chisel3._
    import chisel3.stage.ChiselStage

    /* For Chisel versions <3.2.0 use the following: */
    Driver.emitVerilog(new Foo)

    /* For Chisel >=3.2.0 use the following: */
    (new ChiselStage).emitVerilog(new Foo)
    /* And use more args to specify the target dir */
    (new ChiselStage).emitVerilog(new Hello(), Array("--target-dir", "generated"))
    ```

    最后一行代码会在根目录下的`generated`目录下生成Verilog代码文件

## chisel operators

chisel中有很多针对硬件设计的运算符，可以在[官网](https://www.chisel-lang.org/chisel3/docs/explanations/operators.html)上查看，也可以点击下载[cheat sheet](https://github.com/freechipsproject/chisel-cheatsheet/releases/latest/download/chisel_cheatsheet.pdf)

## program tester

很多时候，我们会在测试文件看到如下表达式:

```scala
import chisel3._
import chisel3.util._
import chisel3.tester._
import chisel3.tester.RawTester.test

test(new MyModule) { c =>
    for (i <- 0 until number) {
        c.io.in.poke(1.U)
        c.io.out.expect(1.U)
    }
}
```

这里就是使用导入的chisel的`test`方法来实例化一个我们的模块，形如`c => ...`是匿名函数，这样我们就可以对一个模块进行测试了

老版本的test语法(iotesters)是这样的:

```scala
val testResult = Driver(() => new Passthrough()) {
  c => new PeekPokeTester(c) {
    poke(c.io.in, 0)     // Set our input to value 0
    expect(c.io.out, 0)  // Assert that the output correctly has 0
    poke(c.io.in, 1)     // Set our input to value 1
    expect(c.io.out, 1)  // Assert that the output correctly has 1
    poke(c.io.in, 2)     // Set our input to value 2
    expect(c.io.out, 2)  // Assert that the output correctly has 2
  }
}
assert(testResult)   // Scala Code: if testResult == false, will throw an error
println("SUCCESS!!") // Scala Code: if we get here, our tests passed!
```

## asynchronous and synchronous

同步(synchronous)和异步(asynchronous)这两个概念会时常出现在chisel中，这里的同步通常指的是有时钟的，时序电路的；而异步通常是指没有时钟的(纯线路)，组合电路的。

## chisel template

如果不想重头新建一个chisel项目，可以下载[chisel-empty](https://github.com/schoeberl/chisel-empty)模版

## debug

可以在chisel线路中写入`println()`来打印想要的值，这样在测试阶段，线路就会打印出运行测试时的值。

e.g.

```scala
class PrintingModule extends Module {
    val io = IO(new Bundle {
        val in = Input(UInt(4.W))
        val out = Output(UInt(4.W))
    })
    io.out := io.in

    printf("Print during simulation: Input is %d\n", io.in)
    // chisel printf has its own string interpolator too
    printf(p"Print during simulation: IO is $io\n")

    println(s"Print during generation: Input is ${io.in}")
}

test(new PrintingModule ) { c =>
    c.io.in.poke(3.U)
    c.clock.step(5) // circuit will print

    println(s"Print during testing: Input is ${c.io.in.peek()}")
}
```

## build-in chisel class

- `Module()`

- `IO()`: 输入输出端口

- `Mux(sel, a, b)`: 多路复选器，相当于`when().elsewhen().otherwise`

- `Mem(size, dataType)`: 创建寄存器的集合(*所以不是DRAM*)，size是个数，dataType是寄存器的数据类型

- `Reverse(data)`: 反转比特序

- `someUInt.zext`: 将一个UInt数据转换成SInt，同时在最高位补一个0

- `Flipped(new MyModuleIO)`: 将一个模块(通常只有输入/输出)分解之后，将Input和Output反转，e.g.

    ```scala
    io.out := MyModuleIO.io.in
    io.in := MyModuleIO.io.out
    ```

- `Decoupled()`: 将信号或者模组加(封装)上`ready`和`valid`信号：

    ```scala
    val in = Decoupled(UInt(8.W))
    // That's to say
    val in = new Bundle{
        val valid = Output(Bool())
        val ready = Output(Bool())
        val bits  = Ouput(UInt(8.W))
    }
    ```

- `MuxCase(default, Aray(c1 -> a, c2 -> b, ...))`: n-way的Mux

- `MuxLookup(idx, default)(Seq(0.U -> a, 1.U -> b, ...))`: 以索引形式添加一个多路的Mux

    类似的有ListLookup和Lookup，两者都是使用`BitPat`的形式来代替索引进行匹配

- `DontCare`

    是一个chisel内置的一个模块，如果你不想给模块指定一个驱动信号，那么可以赋值为`DontCare`，这样就不会报错"not fully initialized"。详见[chisel官网](https://www.chisel-lang.org/chisel3/docs/explanations/unconnected-wires.html)

