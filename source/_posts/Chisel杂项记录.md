---
title: Chisel杂项记录
date: 2024-11-28 19:49:09
updated: 2024-12-24 12:42:00
index_img: https://s2.loli.net/2023/10/01/FXxACeGSV4mIw3L.png
tags:
- HDL
categories:
- 系统底层
---

# Chisel杂项记录

## Module相关

Module是chisel中经常用到的基本类，其作用有两个

1. 作为父类构建一个chisel模块类
2. 使用其单例类来为chisel模块创建上下文，从而在生成系统中构造模块层次

说到底其实就是一个作用：**构造chisel模块**

Module类的底层源码中拥有复杂的继承关系，涉及到的关键类如下：

- `BaseModule`：最底层的抽象类，所有Module的父类
- `BlackBox`：用于创建外部verilog模块，其父类中也包含`BaseModule`
- `RawModule`：`Module`的抽象父类，不包含时钟和复位
- `Module`：最常用到的Chisel模块构造类

除此之外`chisel3.experimental`包中还有[更多用于特殊用途的Module类](https://www.chisel-lang.org/docs/appendix/experimental-features#module-variants)（最新版本好像已经被删除）

## Chisel有符号数据字面量

有符号数据在Chisel中是SInt类型，如果随意使用SInt类型字面量，则可能会混淆数据位宽，这里有必要说明一下。

和UInt类型字面量一样，SInt也遵循位宽为<u>**满足表示该数据的最小位宽**</u>，e.g.

```scala
// Copy from Chisel official document
5.S    // signed decimal 4-bit lit from Scala Int.
-8.S   // negative decimal 4-bit lit from Scala Int.
5.U    // unsigned decimal 3-bit lit from Scala Int.

8.U(4.W) // 4-bit unsigned decimal, value 8.
-152.S(32.W) // 32-bit signed decimal, value -152.
```

也就是说SInt字面量在最高位**补上一位符号位**即可。这并不难以理解，但是在配合使用`MuxLookup`和`asUInt`时可能会让我们犯糊涂：

```scala
io.out := MuxLookup(io.sel, 0.U)(
  Seq(
    -2.S.asUInt -> 1.U,
    2.U -> 2.U
  )
)
```

实际上`-2`和`2`都会被选择为`2.U`，这是因为两者的二进制编码都为`b10`，所以**一定要在包含SInt的key上加上位宽定义**。下面再看一个例子。

```scala
io.out := MuxLookup(io.sel, 0.S)( // io.out has 8.W
  Seq(
    0.U -> -3.S, // change to -3.S(8.W) goes correct
    1.U -> -2.S,
    2.U -> -1.S,
    3.U -> 0.S,
    4.U -> 1.S,
    5.U -> 2.S,
    6.U -> 3.S,
  )
).asUInt
```

这里面同样不会给负数扩展符号位，比如`-3`会被输出为`b0000_0101`(not `b1111_1101`)，但是如果为**其中一个选项设置等于输出的位宽**，就可以使所有选项位宽保持最大位宽。

## Chisel仿真测试

在硬件设计过程中，仿真验证肯定是必不可少的，所以Chisel也准备了相关的仿真方法。

早期Chisel官方使用的是[chiseltest](https://github.com/ucb-bar/chiseltest)这个官方scala包（*更早是iotesters*），不过由于Chisel开发决策的关系（[从Chisel 5开始，核心开发组将Chisel的生成用编译器从SFC换成了MFC](https://www.chisel-lang.org/docs/appendix/migrating-from-chiseltest)），用于Chisel的仿真测试改为使用Chisel内置的[ChiselSim](https://github.com/chipsalliance/chisel/tree/v6.4.0/src/main/scala/chisel3/simulator)方式，原本的ChiselTest已于2024年8月20日停止维护。（*你能看到的大部分Chisel官方相关项目停止维护都是因为ChiselTest*）

下面稍微解释一下两种测试方式的不同：

**ChiselTest**

- 使用内置的仿真器[treadle](https://github.com/chipsalliance/treadle)进行仿真，也可以更改仿真器
- 单独作为Chisel的仿真测试工具进行开发，所以需要额外导入

**ChiselSim**

- [默认仿真器为verilator](https://github.com/chipsalliance/chisel/tree/v6.4.0/svsim)（调用外部api，如果没有安装verilator则无法仿真），后续可能加入vcs
- 作为chisel的内部模块，无需额外导入依赖包

从用法上来说，两者极为相似，都是使用相同的`poke`，`expect`等api对仿真过程加以控制。不过从整体上来说，目前的ChiselSim并不如ChiselTest好用。一方面在于其需要调用外部的verilator仿真器，信息显示不如treadle清晰；另一方面ChiselSim还比较年轻，很多功能都还在开发过程中（*比方说生成仿真波形需要复杂的配置*）。所以Chisel未来的仿真测试究竟如何，还需静待观察。

