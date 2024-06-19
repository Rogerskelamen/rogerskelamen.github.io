---
title: GNU Make
author: Rogers Kelamen
date: 2023-12-22 16:26:10
updated: 2024-05-08 21:42:00
index_img: https://s2.loli.net/2023/12/22/37OATSNeszGcmCu.png
tags:
- c
- linux
categories:
- 工具
---

# GNU Make

我也不知道是什么时候接触到了[make](https://zh.wikipedia.org/wiki/Make)，大概最早可以追溯到第一次在Ubuntu双系统（*本科Linux课程要求*）上编写C程序，然后需要脚本级程序来简化编译。至此在我的编程生涯中，每每有C编程的地方，就有Makefile的身影。从那时开始，我个人一直认为Makefile就是一些简单的脚本规则的堆叠，甚至连编程都算不上，于是就没有必要去深究他的语法规则，更没有必要去整理一份笔记去记录Makefile的使用。但最近我意识到我错了，在阅读他人写好的复杂Makefile文件时，自己对make的肤浅理解导致我痛苦但依旧无法理解他人复杂项目的构建过程。对此，我深深感受到[GNU Make](https://www.gnu.org/software/make/manual/make.html)并非不值得说道的脚本，于是在此补上迟到已久的GNU Make使用笔记。

## Manual

本篇笔记（或者说是博文）并不打算从零开始教授你GNU Make的所有语法，相反，我更推荐你通过官方手册（manual）自行学习：

- [官方手册pdf版](https://www.gnu.org/software/make/manual/make.pdf)

如果觉得官方的手册过于硬核，这里还有一份酷壳创始人陈皓大佬的[非官方手册](https://seisman.github.io/how-to-write-makefile/index.html)

## Makefile典范——AM

为什么你觉得编写Makefile不算编程？为什么有的项目编译过程这么优雅？编译过程中那些`+ CC, + CXX, + LD`是怎么被打印出来的？往往一份优秀的Makefile就可以很好地回答上述问题，而这种Makefile就值得被当作模板来学习和模仿。

[AM](https://github.com/NJU-ProjectN/abstract-machine)（Abstract Machine）项目中的Makefile就是这样的一个模板，它简洁优雅，遵循编程的思维，你甚至可以说它就是一份编程语言源码。

来看看它堪称教科书级的源码吧：

```makefile
# Makefile for AbstractMachine Kernels and Libraries

### *Get a more readable version of this Makefile* by `make html` (requires python-markdown)
html:
	cat Makefile | sed 's/^\([^#]\)/    \1/g' | markdown_py > Makefile.html
.PHONY: html

## 1. Basic Setup and Checks

### Default to create a bare-metal kernel image
ifeq ($(MAKECMDGOALS),)
  MAKECMDGOALS  = image
  .DEFAULT_GOAL = image
endif

### Override checks when `make clean/clean-all/html`
ifeq ($(findstring $(MAKECMDGOALS),clean|clean-all|html),)

### Print build info message
$(info # Building $(NAME)-$(MAKECMDGOALS) [$(ARCH)])

### Check: environment variable `$AM_HOME` looks sane
ifeq ($(wildcard $(AM_HOME)/am/include/am.h),)
  $(error $$AM_HOME must be an AbstractMachine repo)
endif

### Check: environment variable `$ARCH` must be in the supported list
ARCHS = $(basename $(notdir $(shell ls $(AM_HOME)/scripts/*.mk)))
ifeq ($(filter $(ARCHS), $(ARCH)), )
  $(error Expected $$ARCH in {$(ARCHS)}, Got "$(ARCH)")
endif

### Extract instruction set architecture (`ISA`) and platform from `$ARCH`. Example: `ARCH=x86_64-qemu -> ISA=x86_64; PLATFORM=qemu`
ARCH_SPLIT = $(subst -, ,$(ARCH))
ISA        = $(word 1,$(ARCH_SPLIT))
PLATFORM   = $(word 2,$(ARCH_SPLIT))

### Check if there is something to build
ifeq ($(flavor SRCS), undefined)
  $(error Nothing to build)
endif

### Checks end here
endif

## 2. General Compilation Targets

### Create the destination directory (`build/$ARCH`)
WORK_DIR  = $(shell pwd)
DST_DIR   = $(WORK_DIR)/build/$(ARCH)
$(shell mkdir -p $(DST_DIR))

### Compilation targets (a binary image or archive)
IMAGE_REL = build/$(NAME)-$(ARCH)
IMAGE     = $(abspath $(IMAGE_REL))
ARCHIVE   = $(WORK_DIR)/build/$(NAME)-$(ARCH).a

### Collect the files to be linked: object files (`.o`) and libraries (`.a`)
OBJS      = $(addprefix $(DST_DIR)/, $(addsuffix .o, $(basename $(SRCS))))
LIBS     := $(sort $(LIBS) am klib) # lazy evaluation ("=") causes infinite recursions
LINKAGE   = $(OBJS) \
  $(addsuffix -$(ARCH).a, $(join \
    $(addsuffix /build/, $(addprefix $(AM_HOME)/, $(LIBS))), \
    $(LIBS) ))

## 3. General Compilation Flags

### (Cross) compilers, e.g., mips-linux-gnu-g++
AS        = $(CROSS_COMPILE)gcc
CC        = $(CROSS_COMPILE)gcc
CXX       = $(CROSS_COMPILE)g++
LD        = $(CROSS_COMPILE)ld
AR        = $(CROSS_COMPILE)ar
OBJDUMP   = $(CROSS_COMPILE)objdump
OBJCOPY   = $(CROSS_COMPILE)objcopy
READELF   = $(CROSS_COMPILE)readelf

### Compilation flags
INC_PATH += $(WORK_DIR)/include $(addsuffix /include/, $(addprefix $(AM_HOME)/, $(LIBS)))
INCFLAGS += $(addprefix -I, $(INC_PATH))

ARCH_H := arch/$(ARCH).h
CFLAGS   += -O2 -MMD -Wall -Werror $(INCFLAGS) \
            -D__ISA__=\"$(ISA)\" -D__ISA_$(shell echo $(ISA) | tr a-z A-Z)__ \
            -D__ARCH__=$(ARCH) -D__ARCH_$(shell echo $(ARCH) | tr a-z A-Z | tr - _) \
            -D__PLATFORM__=$(PLATFORM) -D__PLATFORM_$(shell echo $(PLATFORM) | tr a-z A-Z | tr - _) \
            -DARCH_H=\"$(ARCH_H)\" \
            -fno-asynchronous-unwind-tables -fno-builtin -fno-stack-protector \
            -Wno-main -U_FORTIFY_SOURCE
CXXFLAGS +=  $(CFLAGS) -ffreestanding -fno-rtti -fno-exceptions
ASFLAGS  += -MMD $(INCFLAGS)
LDFLAGS  += -z noexecstack

## 4. Arch-Specific Configurations

### Paste in arch-specific configurations (e.g., from `scripts/x86_64-qemu.mk`)
-include $(AM_HOME)/scripts/$(ARCH).mk

### Fall back to native gcc/binutils if there is no cross compiler
ifeq ($(wildcard $(shell which $(CC))),)
  $(info #  $(CC) not found; fall back to default gcc and binutils)
  CROSS_COMPILE :=
endif

## 5. Compilation Rules

### Rule (compile): a single `.c` -> `.o` (gcc)
$(DST_DIR)/%.o: %.c
	@mkdir -p $(dir $@) && echo + CC $<
	@$(CC) -std=gnu11 $(CFLAGS) -c -o $@ $(realpath $<)

### Rule (compile): a single `.cc` -> `.o` (g++)
$(DST_DIR)/%.o: %.cc
	@mkdir -p $(dir $@) && echo + CXX $<
	@$(CXX) -std=c++17 $(CXXFLAGS) -c -o $@ $(realpath $<)

### Rule (compile): a single `.cpp` -> `.o` (g++)
$(DST_DIR)/%.o: %.cpp
	@mkdir -p $(dir $@) && echo + CXX $<
	@$(CXX) -std=c++17 $(CXXFLAGS) -c -o $@ $(realpath $<)

### Rule (compile): a single `.S` -> `.o` (gcc, which preprocesses and calls as)
$(DST_DIR)/%.o: %.S
	@mkdir -p $(dir $@) && echo + AS $<
	@$(AS) $(ASFLAGS) -c -o $@ $(realpath $<)

### Rule (recursive make): build a dependent library (am, klib, ...)
$(LIBS): %:
	@$(MAKE) -s -C $(AM_HOME)/$* archive

### Rule (link): objects (`*.o`) and libraries (`*.a`) -> `IMAGE.elf`, the final ELF binary to be packed into image (ld)
$(IMAGE).elf: $(OBJS) am $(LIBS)
	@echo + LD "->" $(IMAGE_REL).elf
	@$(LD) $(LDFLAGS) -o $(IMAGE).elf --start-group $(LINKAGE) --end-group

### Rule (archive): objects (`*.o`) -> `ARCHIVE.a` (ar)
$(ARCHIVE): $(OBJS)
	@echo + AR "->" $(shell realpath $@ --relative-to .)
	@$(AR) rcs $(ARCHIVE) $(OBJS)

### Rule (`#include` dependencies): paste in `.d` files generated by gcc on `-MMD`
-include $(addprefix $(DST_DIR)/, $(addsuffix .d, $(basename $(SRCS))))

## 6. Miscellaneous

### Build order control
image: image-dep
archive: $(ARCHIVE)
image-dep: $(OBJS) am $(LIBS)
	@echo \# Creating image [$(ARCH)]
.PHONY: image image-dep archive run $(LIBS)

### Clean a single project (remove `build/`)
clean:
	rm -rf Makefile.html $(WORK_DIR)/build/
.PHONY: clean

### Clean all sub-projects within depth 2 (and ignore errors)
CLEAN_ALL = $(dir $(shell find . -mindepth 2 -name Makefile))
clean-all: $(CLEAN_ALL) clean
$(CLEAN_ALL):
	-@$(MAKE) -s -C $@ clean
.PHONY: clean-all $(CLEAN_ALL)
```

好的代码往往都是不言自明，不证自明的，AM的Makefile也是这样。代码中的注释基本能解释清楚每个语句的含义和动机以及接下来可能的构建步骤，你甚至可以使用`python-markdown`来将此Makefile转化为html文件，通过浏览器进行更直观地文档式阅读。

### 何善之有？

你可能面对着一百多行的Makefile感到无所适从，尽管你能够查看它的html版本。所以，这个看都看不懂的Makefile到底好在哪里？

1. 它拥有层次分明的结构

   具体来说，先进行环境和基础设施的设置(1)，然后是编译对象(2)，编译选项的和编译工具的设置(3)，编写编译规则运行编译任务(5)，最后定义tags来完成编译任务的控制(6)。

2. KISS

   很显然，它作为顶层的Makefile文件，只是做好了所有的编译选项和编译配置，其他所有的细节都交给`-include`的文件去管理。 

3. 优雅的输出

   这时候就可以回答之前怎么输出`+ CC`的问题了，这里使用的是`@echo + CC $<`进行输出。像这样的优雅表达发生在编译到链接的各个阶段，编译细节永远不会打印出来。至于为什么要命名为`CC、CXX`，请自行了解**隐含规则使用的变量**。

当然，想要完全理解这个文件还需要make变量，make函数，make文件引用，模式规则等知识，以及其他mk文件的补充。这些是你编写了海量make代码之后自然而然就能得到的收获。

## AM到底是啥

Makefile是用来构建C/C++项目的（*其实任何项目都可以*），那么抛开项目本身去谈理解Makefile就显然是一种耍流氓行为。那AM到底是个什么样的项目，或者说它能干啥？

> 应用程序的运行都需要运行时环境的支持; 另一方面, 只进行纯粹计算任务的程序在TRM上就可以运行, 更复杂的应用程序对运行时环境必定还有其它的需求: 例如你之前玩的超级玛丽需要和用户进行交互, 至少需要运行时环境提供输入输出的支持. 要运行一个现代操作系统, 还要在此基础上加入更高级的功能.
>
> 如果我们把这些需求都收集起来, 将它们抽象成统一的API提供给程序, 这样我们就得到了一个可以支撑各种程序运行在各种架构上的库了! 具体地, 每个架构都按照它们的特性实现这组API; 应用程序只需要直接调用这组API即可, 无需关心自己将来运行在哪个架构上. 由于这组统一抽象的API代表了程序运行对计算机的需求, 所以我们把这组API称为抽象计算机.
>
> AM(Abstract machine)项目就是这样诞生的.

以上内容完全摘自南京大学[PA实验讲义](https://ysyx.oscc.cc/docs/ics-pa/2.3.html#am-%E8%A3%B8%E6%9C%BA-bare-metal-%E8%BF%90%E8%A1%8C%E6%97%B6%E7%8E%AF%E5%A2%83)。

简单理解AM就是一个提供不同平台（platform）和上层应用程序（application）的兼容层，它可以使遵守AM API规范的所有应用程序自由地运行于多种ISA和平台上（nemu，npc，native等）。

那么本身AM就天生适配本地环境，可以尝试使用AM编译一个简单C程序：

```makefile
NAME=hello
SRCS=hello.c
export ARCH=native
-include $(AM_HOME)/Makefile
```

使用`make run`就可以运行这个程序。

通过这个实验我们可以了解到，AM实际上就是将输入的`ARCH`进行解耦，完成指令集架构和平台的提取，根据不同的参数分情况进行编译，具体的代码如下(33-36)：

```makefile
### Extract instruction set architecture (`ISA`) and platform from `$ARCH`. Example: `ARCH=x86_64-qemu -> ISA=x86_64; PLATFORM=qemu`
ARCH_SPLIT = $(subst -, ,$(ARCH))
ISA        = $(word 1,$(ARCH_SPLIT))
PLATFORM   = $(word 2,$(ARCH_SPLIT))
```

至此，AM与其Makefile已经在我们眼前格外清晰了，只需要进一步查看不同接口的mk文件就应该能彻底理解AM的功能和它的构建过程。

## Misc

### 内置变量

MAKECMDGOALS：输入`make`命令时所使用的tag名

```makefile
all:
    @echo CURDIR = $(CURDIR)
    @echo PWD = $(PWD)
    @echo .DEFAULT_GOAL = $(.DEFAULT_GOAL)
    @echo MAKECMDGOALS = $(MAKECMDGOALS)
    @echo MAKE = $(MAKE)
    @echo MAKE_VERSION = $(MAKE_VERSION)
    @echo CC = $(CC)
    @echo CXX = $(CXX)
    @echo AS = $(AS)
    @echo LD = $(LD)
    @echo AR = $(AR)
    @echo RM = $(RM)
    @echo
    @echo OUTPUT_OPTION = $(OUTPUT_OPTION)
    @echo CFLAGS = $(CFLAGS)
    @echo CXXFLAGS = $(CXXFLAGS)
```

使用`make all`来查看这些内置变量

**!!注：此列表并不全面，其他内置变量请自行RTFM!!**

### 特殊变量

- $(@F): 表示目标文件的文件名部分（不包括路径）。
- $(^): 表示所有的依赖文件列表。
- $(<): 表示第一个依赖文件。
- $(?): 表示所有比目标文件新的依赖文件列表。
- $(\*): 表示目标文件名去除后缀的部分。
- $(\<D): 表示第一个依赖文件的目录部分。
- $(^D): 表示所有依赖文件的目录部分。
- $(%D): 表示静态模式的目标文件的目录部分。
- $(@D): 表示目标文件的目录部分。

这些特殊变量可用于构建规则中，以方便地获取目标文件、依赖文件等信息。

