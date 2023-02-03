---
title: Arm汇编指令汇总
date: 2023-01-11 15:12:16
index_img: https://s2.loli.net/2023/01/11/an8tljbydCZGIz9.png
tags:
- asm
categories:
- 编程语言
---

# Arm汇编指令汇总

本指令汇总主要由[此文章](https://evian-zhang.github.io/learn-assembly-on-Apple-Silicon-Mac/index.html)总结得出，故若有对指令细节不理解的地方，可将文章作为参考。

*需要说明的是，和AT&T风格的x86汇编相比，arm汇编的源操作数在右边，目的操作数在左边，而且运算指令基本上是需要指定目的地址的。(本文使用的是GUN风格的arm汇编，除此之外还有armasm风格的)*

## 寄存器汇总

> 在AArch64架构下，有31个通用寄存器。这些通用寄存器可以作为大部分指令的操作数参与运算。

r0 ~ r7 : 传递参数或者返回值(第一个参数/返回值对应于r0，以此类推)

r8 : 用来存储多于的参数/返回值，并将其立即存入栈中

r16 : 用来存储系统调用编号(MacOS特有)

r18 : 平台保留的寄存器(Apple Silicon规定，我们不应该使用)

r19 ~ r28 : 被调用者保留的寄存器

r29 : FP寄存器

r30 : LR寄存器

还有一种寄存器比较特殊，叫做零寄存器:

wzr : 读取该寄存器的值，永远为0；向该寄存器写入数值将无效，也就是说无法向该寄存器写入数值

## 赋值指令

| 指令名 | 英文联想       | 指令作用                         |
|--------|----------------|----------------------------------|
| mov    | move           | 将后一个变量值赋值给前一个       |

## 内存交互指令

| 指令名 | 英文联想       | 指令作用                         |
|--------|----------------|----------------------------------|
| ldr    | load register  | 将源地址中的值存储到目的寄存器中 |
| str    | store register | 将寄存器中的值赋值给指定地址中   |

## 扩展指令

| 指令名 | 英文联想                       | 指令作用           |
|--------|--------------------------------|--------------------|
| sxtb   | signed extense with byte       | 有符号扩展一字节   |
| sxth   | signed extense with halfword   | 有符号扩展两字节   |
| sxtw   | signed extense with word       | 有符号扩展四个字节 |
| uxtb   | unsigned extense with byte     | 无符号扩展一字节   |
| uxth   | unsigned extense with halfword | 无符号扩展两字节   |
| uxtw   | unsigned extense with word     | 无符号扩展四个字节 |

## 运算指令

| 指令名 | 英文联想           | 指令作用                                                           |
|--------|--------------------|--------------------------------------------------------------------|
| add    | add                | 将第一个源操作数的值加上第二个源操作数的值返回给目的操作数         |
| sub    | substract          | 将第一个源操作数的值减去第二个源操作数的值返回给目的操作数         |
| and    | and                | 将第一个源操作数的值与上第二个源操作数的值返回给目的操作数         |
| orr    | or                 | 将第一个源操作数的值或上第二个源操作数的值返回给目的操作数         |
| mvn    | move not           | 将源操作数的值取非返回给目的操作数                                 |
| eor    | exclusive or       | 将第一个源操作数的值异或第二个源操作数的值返回给目的操作数         |
| mul    | multiply           | 将第一个源操作数的值乘上第二个源操作数的值返回给目的操作数         |
| smull  | signed multiply    | 将第一个源操作数的值乘上第二个源操作数的值返回给目的操作数(有符号) |
| umull  | unsigned multiply  | 将第一个源操作数的值乘上第二个源操作数的值返回给目的操作数(无符号) |
| sdiv   | signed divide      | 将第一个源操作数的值除以第二个源操作数的值返回给目的操作数(有符号) |
| udiv   | unsigned divide    | 将第一个源操作数的值除以第二个源操作数的值返回给目的操作数(无符号) |
| lsl    | logical shift left | 将第一个源操作数的值左移第二个源寄存器的值返回给目的寄存器         |

## 比较指令

| 指令名 | 英文联想 | 指令作用                 |
|--------|----------|--------------------------|
| cmp    | compare  | 将两数相减，并设置PSTATE |
| tst    | test     | 将两数相与，并设置PSTATE |

## 跳转指令

| 指令名 | 英文联想                   | 指令作用                            |
|--------|----------------------------|-------------------------------------|
| b      | branch                     | 跳转到指定标签处                    |
| br     | branch refer to register   | 跳转到寄存器内的地址处              |
| b.eq   | branch if equal            | 如果相等则跳转                      |
| b.ne   | branch if not equal        | 如果不相等则跳转                    |
| b.hi   | branch if higher           | 如果大于则跳转(无符号比较)          |
| b.hs   | branch if higher or same   | 如果大于等于则跳转(无符号比较)      |
| b.lo   | branch if lower            | 如果小于则跳转(无符号比较)          |
| b.ls   | branch if lower or same    | 如果小于等于则跳转(无符号比较)      |
| b.gt   | branch if greater          | 如果大于则跳转(有符号比较)          |
| b.ge   | branch if greater or equal | 如果大于等于则跳转(有符号比较)      |
| b.lt   | branch if less             | 如果小于则跳转(有符号比较)          |
| b.le   | branch if less or equal    | 如果小于等于则跳转(有符号比较)      |
| bl     | branch and link            | 跳转至指定函数并将其返回地址写入r30 |

## 条件选择指令

| 指令名 | 英文联想           | 指令作用                                                              |
|--------|--------------------|-----------------------------------------------------------------------|
| csel   | conditional select | 根据condition将两个源操作数比较，以此来选择哪个原操作数给到目的操作数 |

## LR、FP寄存器与栈交互

| 指令名 | 英文联想                | 指令作用                                                                |
|--------|-------------------------|-------------------------------------------------------------------------|
| stp    | store pair of registers | 比如`stp  x29, x30, [sp]`表示将x29存储到sp，x30存储到`sp + 8`           |
| ldp    | load pair of registers  | 比如`ldp  x29, x30, [sp]`表示将sp读取8字节至x29，`sp + 8`读取8字节至x30 |

## 系统调用指令

| 指令名 | 英文联想        | 指令作用     |
|--------|-----------------|--------------|
| svc    | supervisor call | 调用系统调用 |

## 参考代码

Fibonacci function:

```armasm
    .p2align    2
fibonacci:
    sub    sp, sp, #32
    stp    x29, x30, [sp, #16]
    add    x29, sp, #16
    str    w0, [x29, #-4]
    cmp    w0, #2
    b.lt   init_val
    sub    w0, w0, #1
    bl     fibonacci
    str    w0, [x29, #-8]
    ldr    w0, [x29, #-4]
    sub    w0, w0, #2
    bl     fibonacci
    ldr    w1, [x29, #-8]
    add    w0, w0, w1
    ldp    x29, x30, [sp, #16]
    add    sp, sp, #32
    ret
init_val:
    mov    w0, #1
    ldp    x29, x30, [sp, #16]
    add    sp, sp, #32
    ret
```

```armasm
# hello.s
    .section    __TEXT,__text
    .globl  _main
    .p2align    2
_main:
    sub     sp, sp, #16
    stp     x29, x30, [sp]

    mov     w0, #0                  ; fd: STDOUT
    adrp    x1, _hello@PAGE
    add     x1, x1, _hello@PAGEOFF  ; cbuf: "hello, world"
    mov     w2, #13                 ; nbyte: 13
    mov     w16, #4                 ; Syscall number: write
    svc     #0x80                   ; write(STDOUT_FILENO, "hello, world", 13);

    ldp     x29, x30, [sp]
    add     sp, sp, #16
    ret

    .data
    .p2align    2
_hello:
    .asciz  "hello, world"
```

*需要注意的是，在macOS中，大部分的系统调用都是可以通过0x80这个数来调用，所以使用`svc #0x80`即可。如果你想知道XNU内核所有的系统调用号，可以参考[这里](https://github.com/apple/darwin-xnu/blob/main/bsd/kern/syscalls.master)。*

