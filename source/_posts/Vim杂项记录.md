---
title: Vim杂项记录
author: Rogers Kelamen
date: 2021-03-11 22:04:02
updated: 2021-09-05 13:06:00
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/Vim_Customization_hubcbgfafafc_catmullrom.jpg
categories:
- Vim
tags:
- geek
---


# Vim杂项记录

> 此处记录有关vim写作过程中遇到的问题或踩过的坑。

## 系统剪切板的通信

Vim 中的复制、删除的内容都会被存放到默认（未命名）寄存器中，之后可以通过粘贴操作读取默认寄存器中的内容。寄存器是完成这一过程的中转站，Vim 支持的寄存器非常多，其中常用的有 a-z A-Z 0-9 +"。其中：

- 0-9：表示数字寄存器，是 Vim 用来保存最近复制、删除等操作的内容，其中 0 号寄存器保存的是最近一次的操作内容。

*a-z A-Z：表示用户寄存器，Vim 不会读写这部分寄存器*

- "（单个双引号）：未命名的寄存器，是 Vim 的默认寄存器，例如删除、复制等操作的内容都会被保存到这里。

- +：剪切板寄存器，关联系统剪切板，保存在这个寄存器中的内容可以被系统其他程序访问，也可以通过这个寄存器访问其他程序保存到剪切板中的内容。

如果想了解更多寄存器内容，可参照[官网文档](https://link.zhihu.com/?target=http%3A//vimdoc.sourceforge.net/htmldoc/change.html%23registers)

通过下面命令可以查看所有寄存器中的内容，也可以只查看指定寄存器的内容（将寄存器名称作为参数）

```vim
:reg [register_name]
```

然后你就能愉快地使用`"+y`来粘贴文本到系统的剪切板了。

### 安装clipboard功能

> 你可能会说，老哥你这个没有用啊！我根本都没看到`"+`寄存器啊！

> 其实我也是这么走过来的……，所以我告诉你，你是没有安装好clipboard的功能。

- 检查vim是否支持clipboard功能:

```bash
vim --version | grep clipboard
```

- 如果有 +clipboard 则跳过这一步; 如果显示的是 -clipboard 说明不支持, 需要

```bash
sudo apt install vim-gtk
```

安装完成之后就可以复制vim文本内容到clipboard了。


## vim按了Ctrl + s后假死的解决办法

使用vim时，如果你不小心按了 <kbd>Ctrl</kbd> + <kbd>s</kbd>后，你会发现不能输入任何东西了，像死掉了一般，其实vim并没有死掉，这时vim只是停止向终端输出而已，要想退出这种状态，只需按 <kbd>Ctrl</kbd> + <kbd>q</kbd>即可恢复正常。
 

## vim的宏录制功能

> 或许我们开始接触vim时会因为vim不能进行多次操作而觉得烦恼（*比如我们想要将每一行的`a`改称`b`*），我们会想这时候有多光标就好了（*然而vim的使用最好是不用鼠标的*）。这时候我们就需要宏了。

### 宏的简单介绍

宏的录制

在 Vim 编辑器中创建新文件或打开现有文件；
按 q 并跟上 宏名 开始录制。假设我们想要将宏名命名为 b ，则按 Esc 和 qb 开始录制；
文本编辑ing……
按 q 停止录制。至此，宏的录制已完成。

宏的运行

按`@`接宏名即可运行宏一次，按次数接@宏名重复运行宏多次。


## vim保存为文件

> 你是否有这样的烦恼，本来我就是使用`vim`来随便敲一下的，但是我发现我急需保存这个我写好的文件，而我却不知道怎么保存

使用`:file filename`来保存你写好的文件，这样它会存在于你当前的目录中

## 使用vim对数字进行操作

按<kbd>Ctrl+a</kbd>可以对数字进行自增(*很智能的哪种，不是只能是个位数*)。同理按<kbd>Ctrl+x</kbd>数字自减
