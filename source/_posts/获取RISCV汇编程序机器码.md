---
title: 获取RISC-V汇编程序的机器源码
date: 2024-01-05 21:15:40
tags:
- asm
categories:
- 编程语言
---

# 获取RISC-V汇编程序的机器源码

## 需求场景

假设有一个riscv assembly文件：

```assembly
addi x1, x0, 1
add  x2, x1, x1
```

如何获取这些assembly的机器码指令？

## 步骤

1. 使用riscv编译器编译这个RV32I（或者你的目标ISA）汇编程序：

   ```shell
   riscv64-linux-gnu-gcc -march=rv32g -mabi=ilp32 -c prog.S
   ```

2. 使用objcopy strip所有的重定义信息和ELF的symbol信息：

   ```shell
   riscv64-linux-gnu-objcopy -S prog.o -O binary prog.bin
   ```

   这里实际上已经得到了实际指令的机器码了，只不过是二进制表示，我们需要ascii来进行阅读

3. 使用hexdump将二进制文件转化为ascii表示：

   ```shell
   hexdump prog.bin
   ```

   当然，因为小端（little endian）的缘故，每两个字节的机器码顺序需要调整，可以使用命令行的数据处理工具进行调整

## 实现的shell脚本

```shell
asm_file=$1
base_file=${asm_file%.*}
obj_file=$base_file".o"
bin_file=$base_file".bin"

riscv64-linux-gnu-gcc -march=rv32g -mabi=ilp32 -c $asm_file

riscv64-linux-gnu-objcopy -S $obj_file -O binary $bin_file

hexdump $bin_file | awk '{$1=""; print $0}' | xargs -n2 | awk '{print $2$1}'

rm -rf $obj_file $bin_file
```
