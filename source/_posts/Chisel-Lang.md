---
title: Chisel Lang
date: 2023-10-01 17:05:23
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

## wired sign

- `???`: 这个<u>三重问号符</u>会主动触发一个`NotImplementedError`

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

- `MuxCase(default, Aray(c1 -> a, c2 -> b, ...))`: n-way的Mux

- `MuxLookup(idx, default)(Seq(0.U -> a, 1.U -> b, ...))`: 以索引形式添加一个多路的Mux
