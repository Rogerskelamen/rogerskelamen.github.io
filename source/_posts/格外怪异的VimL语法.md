---
title: 格外怪异的VimL语法
date: 2022-06-09 14:23:01
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/06/09/XlJ1sFvu6pUoxDd.png
tags:
- geek
categories:
- Vim
---

# 格外怪异的VimL

> 本文主要讲解VimL(Vim Language)，即vim脚本语言语法的怪异之处(*即使是相较于其他脚本语言也很怪异*)

## Key Word

> 主要分为这样9类(像shell脚本一样，如果想要输出某变量则使用`echo`)

1. let call (unlet)

2. if for while function try (endif, endfor, end...)

3. break continue return finish

4. echo echomsg echoerr

5. execute normal source

6. set map command

7. augroup autocmd

8. wincmd tabnext

9. 其他着重于编辑功能的命令

## 变量

在VimL中有几种不同的变量:

1. 以`g:`, `l:`, `s:`等前缀开头的vim脚本变量

  需要注意的是`v:`是VimL中内置的变量，一般不要修改

2. 以`$`开头的环境变量

  如`$HOME`, `$MYVIMRC`

3. 以`&`开头的选项值变量

  如`&filetype`, `&number`

### 变量类型

> 身为脚本语言，其变量类型还是类似于python那几种

![](https://s2.loli.net/2022/06/09/tMCWasZdvEVRFDj.png)

最为常用的还是`number`, `string`, `list`, `dict`

### 字符串连接

所有脚本字符串连接都是`+`，而VimL是`.`

### 前缀变量解释

实际上VimL对前缀变量的存储方式是每一个不同作用域的变量存储在一个同一个字典dict中

如`g:`就是一个字典

![let g:a = 1之后查看g:](https://s2.loli.net/2022/06/09/dqgJ8hNcC6pBP5H.png)

## 注释

首先使用`"`是作为行注释没有问题，但是我们要是写注释最好就使用`|"`

原因: **VimL中，每一行就是一条ex执行语句，那么注释最好就是在一条ex执行语句中，也就是单独成行。如果需要在执行语句之后写注释，那么最好就加上`|`用来分别语句**

简答结论：<u>行尾注释使用`|"`</u>

## 多行执行单行语句

普通的脚本语言都是在当前行尾使用`\`对换行进行转义，但是VimL都是一行一行执行语句，没有换行符的概念，所以多行执行就需要在下一行行首加上`/`进行说明，连接上一行语句

## 比较运算符

在字符串的比较过程中，我们一般采用`==`和`!=`来对两边字符串是否相等进行比较。

但是如果用户有自定义设置`set ignorecase`这种大小写敏感的话，我们就需要注意进一步使用比较运算符：

1. `==#`: 不管用户配置，始终以大小写敏感来比较

2. `==?`: 不管用户配置，始终以大小写不敏感来比较

简单总结：<u>使用`==#`或者`!=#`进行字符串的比较</u>

## 函数Function

### 覆盖同名函数

注意使用`!`来覆盖之前可能出现同名的函数，如：

```vim
function! Sum(x, y)
  return a:x + a:y
endfunction
```

### 函数命名规范

如果要声明一个新的函数，一定要用首字母大写来命名函数

<u>因为Vim内置的函数全部都是用小写命名函数，所以使用首字母大写以示区分</u>

