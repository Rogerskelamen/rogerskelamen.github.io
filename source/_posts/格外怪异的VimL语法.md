---
title: 格外怪异的VimL语法
date: 2022-06-09 14:23:01
updated: 2022-11-05 14:48:00
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/06/09/XlJ1sFvu6pUoxDd.png
tags:
- geek
categories:
- Vim
---

# 格外怪异的VimL

> 本文主要讲解VimL语法(Vim Language)，你绝对无法想象VimL有多怪(*即使是相较于其他脚本语言也很怪异*)

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

`type()`函数可以返回变量的类型

最为常用的还是`number`, `string`, `list`, `dict`

### 创建变量

因为是脚本语言，所以声明变量统统用`let`:

```vim
let num = 1
let str = 'hello, world'
let list = [0, 1, 2, 3]
let dict = {'x': 1, 'y': 2, 'z': 3,}
```

*Note: 和javascript类似，dict可以使用['key']和.key的方式访问和更改value*

#### 删除变量

在vimL中，因为语言服务于编辑器，所以存在"删除变量"这一说法。

需要使用`unlet`删除：

```vim
unlet list[1] | echo list
unlet list[-1] | echo list
unlet dict['u'] | echo dict
unlet dict.v | echo dict
```

### 字符串连接

所有脚本字符串连接都是`+`，而VimL是`.`

### 前缀变量解释

常见作用域前缀介绍：

1. g: 全局作用域。全局变量就是在当前 vim 会话环境中，在任何脚本，任何 ex 命令行中都可以引用的变量。所有在函数之外的命令语句，都默认是全局变量。

2. l: 局部作用域。只可在当前执行的函数体内使用的变量，在函数体内的变量默认为局部变量，l: 局部变量也只能在函数体内使用。

3. s: 脚本作用域。只有当前脚本内可引用的变量，包括该脚本的函数体内。

4. a: 参数作用域。特指函数的参数，在函数体内，要引用传入的实参，就得加上 a:前缀，但定义函数时的形参，不能加 a: 前缀。a: 还隐含一个限定是只读性，即不能在函数体内修改参数

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

*我们还看到过`=~#`和`!~#`这个意思就是进行正则匹配是否成功*

### 使用empty函数

在很多时候，我们需要判断一个变量是否为空，由于vimL的特性，导致很多烧脑的逻辑，所以直接使用`empty()`来进行判空即可(当变量是空时为true)

## 遍历

在遍历这一节，vimL显得不那么怪异了，它和大部分脚本语言一样：

```vim
" for list
for item in list
  echo item
endfor
" for dict
for [key, val] in items(dict)
  echo key . '=>' . val
endfor

```

*需要注意的是，dict变量需要使用`items()`来获取键值对列表，当然也可以只获取keys和vals:*

```vim
for key in keys(dict)
  echo key . '=>' . dict[key]
endfor
for val in values(dict)
  echo val
endfor
```

你也可以先对键排个序：

```vim
for key in sort(keys(dict))
  echo key . '=>' . dict[key]
endfor
```

### 固定循环次数

在这里vimL和python很像，都是使用`range()`:

```vim
for _ in range(5)
  echo 'Hello World!'
endfor
```

## 函数Function

### 覆盖同名函数

注意使用`!`来覆盖之前可能出现同名的函数，如：

```vim
function! Sum(x, y)
  return a:x + a:y
endfunction
```

### 使用不定长的参数

有时候你会看见类似于Funcname(arg1, arg2, ...)的函数定义

这里的`...`其实就是我们通常的不定长参数，我们可以使用很多的参数追加到参数表中，并在函数体中使用`a:{index}`的方式来指代不定长参数

*a:1 表示 ... 部分的第一个参数，a:2 表示第二个，如此类推，a:0 用来表示 ... 部分共有多少参数*


### 函数命名规范

如果要声明一个新的函数，一定要用首字母大写来命名函数

<u>因为Vim内置的函数全部都是用小写命名函数，所以使用首字母大写以示区分</u>

### 函数统一规范

如果我们想让我们定义的函数出错后自动停止执行，需要加上`abort`函数属性

```vim
function! Foo() abort
  ...
endfunction
```

*如果我们不想函数向不可预估的后果上发展，最好就都加上`abort`*

#### 使用autoload

在VimL的函数命名规范中，如果我们想要vim在加载中自动载入我们的一个函数，那么我们这个函数的命名就需要如下规范：

```vim
function! filename#funcname() abort
```

Vim 在 'runtimepath' 里的 "autoload" 目录搜索
脚本文件 "filename.vim"。例如 "~/.vim/autoload/filename.vim"。

文件名和函数的 # 之前的名字必须完全匹配，而定义的函数名也必须和调用时使用的形式完全一致。

<u>使用`:h autoload`来了解更多</u>

## 自定义命令

使用`command`命令来自定义属于你的命令

```vim
command {cmd} {rep}
```

*使用`command!`将之前同名的指令覆盖*

### 命令属性

主要介绍一下参数个数

- -nargs=1 仅接受一个参数
- -nargs=\* 接受 0 或多个参数
- -nargs=? 接受 0 或 1 个参数
- -nargs=+ 接受 1 或多个参数

## execute 和 normal

- `execute`顾名思义，就是执行某个指令

```vim
execute {exp}
```

比如`:execute 'map x y'`相当于直接执行命令`:map x y`

*execute的作用在于，它可以识别变量，于是我们就可以动态地执行命令了*

- `normal`代表用户在普通模式下按下的键位来执行的命令

```vim
normal {command}
```

需要注意的是，normal之后不能写表达式，它代表的意义仅仅是我在普通模式下按下了哪些键位

*注意：使用`normal!`来指明之后的指令不受映射的影响(所以你最好都加上`!`)*

<u>当然，你完全可以把execute和normal联合起来用</u>

## autocmd指令

`autocmd`顾名思义，就是自动化指令，可以使用这个关键字来完成自动化指令的定义。

如果你看过一些关于vim的教程，可以看到以下autocmd格式：

```vim
autocmd [group] event pattern [nested] command
```

- group: 将一组自动化命令归为一类，然后统一管理（比如之后进行删除等操作）

- event: 用来触发自动化指令的事件，这种事件多达80多种，包括(`BufNewFile`, `VimEnter`, `FileType`等)

- pattern: 格式上是一个文件类型名，用来指明对哪几种文件生效。你可能在很多其他人的vim配置文件中看到，类似于`autocmd VimEnter * ...`。这里的`*`意思就是说对于所有的文件类型都生效，即"我只要打开了vim就要生效"

- nested: 如果存在，就可以嵌套地编写自动化指令了

- command: 这里就可以写指令了

如果在使用vim的特定时间点，我们要删除一个自动化指令怎么办？

```vim
autocmd! [group] [event] [pattern]
```

就可以根据组，事件，或者文件进行删除了

## 特殊代替符号

老实说我也不知道这些symbol应该叫什么名字，这些特殊的symbol主要有(你可能会看到的):

- `<cword>`: 代表着当前光标下的单词

- `<cWORD>`: 代表着当前光标下的字符串(打破了不是单词的局限)

- `<cfile>`: 当前光标下的路径名

## 常用VimL特殊变量(v:)

> 使用`echo v:`就可以查看到有哪些vim内置变量了(是一个dict类型)

现在介绍几个可能用到的变量:

- v:insertmode

  用于InsertEnter和InsertChange自动命令事件。取值:

  - i	插入模式
  - r	替换模式
  - v	虚拟替换模式


## 常用VimL函数

> 这些函数在你编写VimL脚本的时候很有用，或者你在看别人的vim脚本的时候这些函数也很常见。

- get()

  这个函数有很多的重载，可以对list，blob，dict操作，我们简单看看对list和dict的操作：

  get({list}, {idx} [, {default}])

  获取 {list} 的第 {idx} 个项目。如果不存在此项目，返回 {default}。如果省略 {default}，返回零。

  get({dict}, {key} [, {default}])

  获取 {dict} 键为 {key} 的项目。如果不存在此项目，
  返回 {default}。如果省略 {default}，返回零。有用的例子: 

  ```vim
  let val = get(g:, 'var_name', 'default')
  " 由于`g:`本身就是一个dict，所以就是查看全局是否有`var_name`存在
  ```

  如果 g:var_name 存在，返回它的值，如果不存在返回 'default'。

- has({feature} [, {check}])

  如果 {check} 省略或为零: 返回数值，如果支持特性 {feature} 则为1，不然为零。{feature} 参数是大小写忽略的字符串。

  主要有三种类型的特性：

  1. 只有在 Vim 编译时加入才会支持的特性 +feature-list
  2. 只有特定条件满足才会支持的特性，如has("gui_running")是查看是否正在运行gvim
  3. 特定版本之后或在特定版本上并包含了特定补丁。"patch-7.4.248" 特性意味着 Vim版本是 7.5 或之后，或 7.4 并包含了补丁 248

- add({object}, {expr})

  在 List 或 Blob {object} 最后附加项目 {expr}。返回新产生的List 或 Blob。如：

  ```vim
  :let alist = add([1, 2, 3], item)
  :call add(mylist, "woodstock")
  ```

- bufnr([{buf} [, {create}]])

  返回缓冲区的编号，如同 ":ls" 命令显示的那样。
  如果 {buf} 省略使用当前缓冲区。
  如果 {buf} 参数是数值，指定缓冲区号。数值零代表当前窗口的轮换缓冲区。
  如果 {buf} 参数是字符串，它用作 |file-pattern| 来匹配缓冲区名字。这里总假设置位 'magic' 而 'cpoptions' 为空。如果有超过一个匹配，返回空字符串。

  如果不存在符合的缓冲区，返回 -1。或者，如果提供了 {create} 参数而且为 TRUE，建立一个新的列表外缓冲区，并返回其编号。例如:

  ```vim
  let newbuf = bufnr('Scratch001', 1)
  ```

  特别的，bufnr("$") 是最后一个缓冲区:

  ```vim
  :let last_buffer = bufnr("$")
  ```

  *"" 或 "%" 可用来指定当前缓冲区，"#" 指定轮换缓冲区。*

- winnr([{arg}])

  返回数值，当前窗口的编号。最上面的窗口的编号为 1。弹出窗口返回零。

  可选参数 {arg} 支持以下值:

  - `$`	返回最后一个窗口的编号 (即窗口的总数)。
  - `#`	返回最近访问的窗口号 (CTRL-W_p 到的地方)。如果没有上次窗口或它在另一个标签页中，返回 0。
  - `{N}j`	当前窗口之下第 N 个窗口的窗口号 (CTRL-W_j 到的地方)。
  - `{N}k`	当前窗口之上第 N 个窗口的窗口号 (CTRL-W_k 到的地方)。
  - `{N}h`	当前窗口之左第 N 个窗口的窗口号 (CTRL-W_h 到的地方)。
  - `{N}l`	当前窗口之右第 N 个窗口的窗口号 (CTRL-W_l 到的地方)

- nr2char({expr} [, {utf8}])

  返回单个字符组成的字符串，该字符的数值为 {expr}。例如:

  ```vim
  nr2char(64)	返回 "@"
  nr2char(32)	返回 " "
  ```

  如果 {utf8} 省略或为零，使用当前的 'encoding'

- expand({string} [, {nosuf} [, {list}]])

  扩展 {string} 里的通配符和下列特殊关键字。
  如果扩展失败，返回空字符串。如果 {string} 以 '%'，'#' 或 '<'开始，不返回不存在的文件名
  如果 {string} 以 '%'、'#' 或 '<' 开始，以类似于cmdline-special变量的方式扩展，包括相关的修饰符。这里是一个简短的小结:

  ```vim
  %		当前文件名
  #		轮换文件名
  #n		轮换文件名 n
  <cfile>		光标所在的文件名
  <afile>		自动命令文件名
  <abuf>		自动命令缓冲区号 (以字符串形式出现！)
  <amatch>	自动命令匹配的名字
  <cexpr>		光标所在的 C 表达式
  <sfile>		载入的脚本文件或函数名
  <slnum>		载入脚本文件行号或函数行号
  <sflnum>	脚本文件行号，即使在函数中亦然
  <SID>		"<SNR>123_"  其中 "123" 是当前脚本ID <SID>
  <script>	执行中的脚本文件，或当前函数定义所在的脚本文件
  <stack>		调用栈
  <cword>		光标所在的单词
  <cWORD>		光标所在的字串 (WORD)
  <client>	最近收到的消息的 {clientid} server2client()
  ```

  修饰符:
  ```vim
  :p		扩展为完整的路径
  :h		头部 (去掉最后一个部分)
  :t		尾部 (只保留最后一个部分)
  :r		根部 (去掉一个扩展名)
  :e		只有扩展名
  ```

  例如:
  ```vim
  :let &tags = expand("%:p:h") .. "/tags"
  ```

- filter({expr1}, {expr2})

  {expr1} 必须是 List 、String、Blob 或者 Dictionary。对 {expr1} 里的每个项目计算 {expr2}，如果结果为零或假值，从该List 或 Dictionary 里删除该项目。

  如果 {expr2} 是 string，{expr2} 内的 v:val 包含当前项目的值。Dictionary 中 v:key 包含当前项目的键。List 中v:key 包含当前项目的索引。

  eg.

  ```vim
  call filter(mylist, 'v:val !~ "OLD"')
  " 删除所有出现 "OLD" 的项目
  call filter(mydict, 'v:key >= 8')
  " 删除所有键小于 8 的值
  ```

- index({object}, {expr} [, {start} [, {ic}]])

  寻找 {object} 中的 {expr}，并返回其索引。

- values({dict})

  返回{dict} 的所有值(list形式)

- keys({dict})

  返回{dict} 的所有键(list形式)

- items({dict})

  返回{dict} 的所有键-值组对(list形式)

- fnamemodify({fname}, {mods})

  根据 {mods} 修改文件名 {fname}。{mods} 是一个字符序列组成的字符串，就像命令行上使用的文件名那样。详见filename-modifiers。例如:

  ```vim
  :echo fnamemodify("main.c", ":p:h")
  ```
  返回:/home/user/vim/vim/src

- jobstart({cmd} [, {opts}])

  使用{cmd}来产生一个任务，如果{cmd}是一个String就直接在shell上运行{cmd}。

- match({expr}, {pat} [, {start} [, {count}]])

  如果 {expr} 是 List，返回匹配 {pat} 的第一个项目的索引。否则，{expr} 用作字符串。返回数值，给出 {expr} 里 {pat} 匹配的(字节计算的偏移量) 位置。

  在第一个字符或 List 项目上的匹配返回零。若无匹配，返回 -1。

  ```vim
  :echo match("testing", "ing") " 返回 4
  :echo match([1, 'x'], '\a')   " 返回 1
  ```

- count({comp}, {expr} [, {ic} [, {start}]])

  返回String、List 或 Dictionary {comp} 里值为 {expr} 的项目出现的次数。
