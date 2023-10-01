---
title: riscv assembly的调用规约
date: 2023-08-06 18:17:59
updated: 2023-08-07 11:11:00
index_img: https://s2.loli.net/2023/08/06/6N2dLJXTn7gD15z.png
banner_img: https://s2.loli.net/2023/08/06/dMuP2W7oVDfiet4.png
tags:
- asm
categories:
- 编程语言
---


# riscv assembly的调用规约

本来最开始学习riscv assembly的时候只是抱着简单了解学习的态度，但是学习过程中真的收获不少，该说不愧是riscv发源地吗(学习的是UC Berkeley的CS61C)，让我悟到了汇编编程的思路，其中calling convention(调用规约)给我留下了深刻印象。我要是早点接触到这个概念就好了，本科阶段的x86汇编真的让我学的晕头转向的(国内的教材也是一言难尽)。下面是riscv的简单介绍。

> riscv是一种不同于传统x86和arm全新的指令集架构(ISA)，由UC Berkeley于2010发起创立。其指令集架构极其简单，因为其遵循极致的[RISC](https://zh.wikipedia.org/wiki/%E7%B2%BE%E7%AE%80%E6%8C%87%E4%BB%A4%E9%9B%86%E8%AE%A1%E7%AE%97%E6%9C%BA)理念，核心指令只有50条左右，非常适合初学者理解学习。

总之calling convention是很重要的概念而且经常被人忽视，所以在此编写文章加以记录。其他的内容比如寄存器或是指令的使用就不在此介绍，如有需要可以参考UC Berkeley的[greencard](https://inst.eecs.berkeley.edu/~cs61c/resources/riscvcard-large.pdf)加以学习了解。

*ps: [greencard](https://inst.eecs.berkeley.edu/~cs61c/resources/riscvcard-large.pdf)只是用作学习时的参考，内容不会很全面。如果是正式的riscv开发，请参考[官方手册](https://riscv.org/wp-content/uploads/2016/06/riscv-spec-v2.1.pdf)*

## 规约简介

其实调用规约在很多ISA的规范文档上都存在，比如intel的x86调用规约通常叫做[cdecl](https://zh.wikipedia.org/wiki/X86%E8%B0%83%E7%94%A8%E7%BA%A6%E5%AE%9A)。简单解释一下调用规约为什么存在：

每当你调用函数的时候(*其实就是跳转到另一段代码*)，你会在被调用的函数中使用部分寄存器(*比如s0, s1*)，而这些寄存器有可能在调用的函数中已经被使用了，那么你要是使用这些寄存器而不进行保存的话，原来调用的寄存器中的值就会被覆盖掉。于是我们得对两者都会使用的寄存器做保存，但是保存的工作到底是交给调用的函数还是被调用的函数呢？这就需要我们做出规约。

## 基本格式

作为被调用者(callee)，我们需要保存的是所有的saved registers，也就是s0～s11；而作为调用者(caller)，需要保存的是所有的temporary registers(t0~t6)，ra和argument registers(a0~a7)。想要看有哪些寄存器是给被调用者保存还是给调用者保存，还是清参考[greencard](https://inst.eecs.berkeley.edu/~cs61c/resources/riscvcard-large.pdf)。

![riscv calling convention](https://s2.loli.net/2023/08/06/iIaG3ULfo2EBZz5.png)

比如现在我正在编写一个函数func，它使用到了s0和s1，而调用它的函数同时也用到了这两个寄存器，那么我就要在这个函数的开头和结尾进行保存:

```asm
addi sp, sp, -8
sw s0, 0(sp)
sw s1, 4(sp)
...
lw s0, 0(sp)
lw s1, 4(sp)
addi sp, sp, 8
```

同时，func函数也调用了另一个函数foo，函数foo用到了func函数中的t0，那么我就得将t0和ra也保存起来，方法和上面一样，不同的是需要在调用函数的前后编写套话(prologue,epilogue):

```asm
addi sp, sp, -8
sw ra, 0(sp)
sw t0, 4(sp)
jal ra, foo
lw t0, 0(sp)
lw ra, 4(sp)
addi sp, sp, 8
```

## 注意事项

这个规约虽然看起来很简单，但是用起来就有些头疼了，比如只要调用者使用了saved register函数，那我都要在被调用者中保存吗？显然不是，这里提供一些注意事项：

1. **如果saved registers在调用函数f之后没有再使用过，那么f就没有必要保存saved registers，同样也适用于temporary registers**

2. **如果这个函数既是调用者(caller)又是被调用者(callee)，那么就必须要保存ra**

3. **如果这个函数不再去调用任何函数，可以使用temporary registers来代替saved registers的使用，这样可以省去保存步骤**

当然，我们编程的环境复杂多变，可能会遇到上述tips也无法解决情形，那就需要自己去仔细分析，安排好caller和callee的寄存器保存。

*ps: 想要领悟更多关于调用规约的思路，可以参考[这篇文章](https://inst.eecs.berkeley.edu/~cs61c/su20/projects/proj2/Understanding_RISC_V_Calling_Convention1.pdf)*

# 使用U-type指令

U代表的是(Upper)，操作的是高位20位的立即数，这类指令(主要就是lui, auipc)弥补了我们不能表示32位立即数的缺点。值得注意的是，U-type指令只能操作高位，也就是说低位的12位需要我们自己加上，这就出现一个问题：低位数会被符号扩展(sign extension)导致结果数据不是我们想要的。比如:

```asm
lui x10, 0xDEADB
addi x10, x10, 0xEEF
```

最终的x10内的数据会是0xDEAD<font color=red>A</font>EEF，因为addi指令中的0xEEF被符号扩展为0xFFFFFEEF，所以相加之后就会变成0xDEAD<font color=red>A</font>EEF，而我们预想的数据结果本该是0xDEAD<font color=red>B</font>EEF

想要解决很简单，只要稍微在编写代码时思考一下，将高位20位的立即数加一(0xDEADC)即可。只是我们每次在单独使用U-type指令时，要多思索一下。
