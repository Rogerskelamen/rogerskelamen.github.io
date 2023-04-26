---
title: Grub引导进入Windows系统
date: 2021-10-22 10:12:08
author: Rogers Kelamen
tags:
- miscellaneous
categories:
- 操作系统
---

# Grub 进入Windows

> 因为双系统删除了Ubuntu，果不其然出现了grub启动残留，导致每次不是直接进入Windows而是进入grub，对这种情况，还可以直接使用Grub挂载引导项(*命令行大法好*)

可参考[链接🔗](https://www.zhihu.com/question/66708983)

## Steps

1. 首先设置(install)相应的模块:

```shell
insmod chain
insmod ntfs
```

2. 然后设置进入boot的默认root：

```shell
set root=(hd0,0)
```

*记住逗号之间没有空格!*

这里可以直接使用`Tab`补全

> 什么意思？

这个`hd0`是指那个磁盘（不是盘符），`0`是指哪个分区

**即一个是disk，一个是partition**

3. 使用`chainloader`指令载入Windows的boot

```shell
chainloader +1
```

*当你出现Invalid EFI file path错误的时候，使用下面的一条，因为适用于Windows的GRUB条目适用于BIOS模式引导，但不适用于EFI模式引导。这是一个GRUB错误。*

*就是说这条命令只适合BIOS安装的系统，但是我是EFI+GPT安装的双系统，所以会出错。*

```shell
chainloader /EFI/Microsoft/Boot/bootmgfw.efi
```

4. 最后boot

```shell
boot
```

## `diskpart`

`diskpart`是Windows操作系统中提供的一个磁盘管理的命令行服务。(*在Windows下显得还有点良心*)

```shell
list disk   # 查看磁盘
select disk 0 # 选择0号磁盘
list partition # 查看所选磁盘的分区
select partition 1 # 选择1号分区
delete partition override # 删除所选分区
assign letter = p # 给所选分区设置盘符取名为p
```

