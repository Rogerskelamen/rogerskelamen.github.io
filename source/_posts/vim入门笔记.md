---
title: Vim入门笔记
date: 2020-12-21 22:51:10
updated: 2023-01-01 21:41:00
index_img: https://s2.loli.net/2022/03/27/NyTDUXWFqjLuM9r.png
banner_img: https://s2.loli.net/2022/03/27/ptrzIUA62G74w1e.png
author: Rogers Kelamen
tags:
- geek
categories:
- Vim
---


# Vim入门笔记

> 啥都不说，献上键位表示

![](https://s2.loli.net/2022/03/26/JsL9PmvlH8RGuDA.jpg)

# 键位简化

> 将以上复杂的键位入门化

| 按键         | 单词表示   | 作用                                        |
| ------------ | --------   | ---------------------------------------     |
| <kbd>w</kbd> | word       | 向后一个词                                  |
| <kbd>b</kbd> | back       | 向前一个词                                  |
| <kbd>a</kbd> | append     | 在这个字符后写入                            |
| <kbd>A</kbd> | 同上       | 在本行末开始写入                            |
| <kbd>i</kbd> | insert     | 在光标位置插入                              |
| <kbd>I</kbd> | 同上       | 在本行首开始写入                            |
| <kbd>o</kbd> | open       | 创建下一空行并写入                          |
| <kbd>O</kbd> | 同上       | 创建上一空行并写入                          |
| <kbd>y</kbd> | yank       | 复制当前字符                                |
| <kbd>Y</kbd> | 同上       | 复制当前字符及其行后内容                    |
| <kbd>c</kbd> | change     | 根据之后的方向删除当前字符并写入            |
| <kbd>C</kbd> | 同上       | 删除当前字符及其行后内容并写入              |
| <kbd>d</kbd> | delete     | <u>剪切</u>当前字符                         |
| <kbd>D</kbd> | 同上       | <u>剪切</u>当前字符及其行后内容             |
| <kbd>s</kbd> | substitute | 删除当前字符并进入插入模式                  |
| <kbd>s</kbd> | 同上       | 删除当前行并进入插入模式                    |
| <kbd>x</kbd> | 暂未知     | 删除光标所在字符                            |
| <kbd>x</kbd> | 暂未知     | 删除光标所在位置的前一个字符                |
| <kbd>p</kbd> | paste      | 将剪切板上的内容粘贴                        |
| <kbd>f</kbd> | find       | 将光标移至“f”按键之后的字符（向后查找最近） |
| <kbd>F</kbd> | 同上       | 将光标移至“F”按键之后的字符（向前查找最近） |
| <kbd>g</kbd> | go         | 将光标移动到一个位置（动作键）              |



<font size=4>**常用键位组合：**</font>

<kbd>dd</kbd>：删除当前行

<kbd>gg</kbd>：跳转到<u>文本</u>首行

<kbd>G</kbd>：跳转到<u>文本</u>末尾行

<kbd>ciw</kbd>：更改光标所在单词的内容（change in word）

<kbd>ci"</kbd>：删除光标所在双引号中的内容并写入

<kbd>yi"</kbd>：类比上面的，有复制光标所在双引号中的内容

<kbd>zz</kbd>：使视野居中

<kbd>df\*</kbd>：找到指定字符并从光标处一直删除到那个字符（\*为任意字符）

<kbd>ggvG=</kbd>：格式化文档（对你没听错）

<kbd>Ctrl</kbd>+<kbd>o</kbd>：回到上一次更改的位置（<u>**可跨文件**</u>）

<kbd>Ctrl</kbd>+<kbd>i</kbd>：回到回到后上次更改的位置（<u>**可跨文件**</u>）

<kbd>gf</kbd>：直接用vim打开光标所在的这个目录文件

<kbd>Ctrl</kbd>+<kbd>a</kbd>：若当前字符是数字，这个数字加1

<kbd>Ctrl</kbd>+<kbd>x</kbd>：若当前字符是数字，这个数字减1

<kbd>Ctrl</kbd>+<kbd>]</kbd>：如果当前光标位于标识符上，跳转到帮助文档的相应页上

<kbd>\*</kbd>：正向搜索光标所在标识符

<kbd>\#</kbd>：反向搜索光标所在标识符

# vim指令

> 在normal模式（普通模式）下，我们可以先输入`:`然后就可以输入vim的指令了。利用vim指令可以实现更多的操作功能。

## 保存和退出

最基本的操作，`w` for `write`，`q` for `quit`：

```vim
:wq
```

*ps：`:`之后的指令可以连在一起写多个，这里的wq就是‘写入’和‘退出’*

***按`!`之后是强制执行，如`q!`就是强制退出***

这个我们配置一下后就用<kbd>S</kbd>保存，<kbd>Q</kbd>退出：

```vim
map S :w<CR>
map Q :q<CR>
```

## vim内打开文件

`e` for `edit`，eg：

```vim
:e ~/program
```

然后就会出现一个分屏窗口（一个简易的文件管理UI），进行文件的选择，最后选择（按回车）后就会出现这个文件的分屏

## vim内执行shell命令

直接上例子（用`!`来告诉vim之后的指令是shell指令）：

```vim
:! ls
```

这样就会使终端回到shell中执行命令并可看得到结果

![](https://s2.loli.net/2022/03/26/xp25zcTLXG4kghn.png)

*然后vim会提示你按回车回到vim*

### 扩展

>  你要是想要用shell执行多个命令然后回到vim，还可以这么操作：

1. 先按<kbd>Ctrl</kbd>+<kbd>z</kbd>然后就会退出vim来到shell，同时vim会后台程序中
2. 我们再输入我们要执行的shell指令
3. 最后用`fg`命令回到vim

# 本人的.vimrc配置

## 有注释版

```vim
"------------------------------
"设置leader映射,空格为leader键
"------------------------------
let mapleader=" "
noremap <LEADER><CR> :nohlsearch<CR>
noremap <LEADER>S :split<CR>
noremap <LEADER>V :vsplit<CR>
" leader加上方向键控制分屏移动
map <LEADER>l <C-w>l
map <LEADER>k <C-w>k
map <LEADER>h <C-w>h
map <LEADER>j <C-w>j
"按两次空格之后跳到下一格<++>然后写入 
map <LEADER><LEADER> <Esc>/<++><CR>:nohlsearch<CR>c4i

"----------------------------
"普通键位映射
"----------------------------
map S :w<CR>
map Q :q<CR>
noremap H 5h
noremap J 5j
noremap K 5k
noremap L 5l
" 方向键控制分屏大小
map <up> :res -5<CR>
map <down> :res +5<CR>
map <left> :vertical resize-5<CR>
map <right> :vertical resize+5<CR>
map th :-tabnext<CR>
map tl :+tabnext<CR>
map tu :tabe<CR>j
" w通过s加v/j来更改分屏布局
map sv <C-w>t<C-w>H
map sj <C-w>t<C-w>K
"切换拼写错误的检验
map <LEADER>sc :set spell!<CR>

"----------------------------
"全局的部分配置
"----------------------------
set enc=utf8
set fencs=utf8,gbk,gb2312,gb1803
set nocompatible		"设置兼容
filetype on		"以下都是相关文件类型配置
filetype indent on
filetype plugin on
filetype plugin indent on
set mouse=a		"你可以在vim中使用鼠标
set encoding=utf-8
let &t_ut=''
set scrolloff=5		"使得时刻可以在开头和末尾有五行显示
set autochdir		"设置当前文件目录下执行命令
" 按leader键和w清除所有的行尾空格
noremap <leader>w :%s/\s\+$//<cr>:let @/=''<CR>
"记住上一次打开的光标位置
au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif

"------------------------------
" Vim UI
"------------------------------
set number		"显示行号
set relativenumber		"相对地显示行号
set cursorline		"展示当前行下划线
set wrap		"一行内容超出换行
set wildmenu		"在指令模式下有Tab提示
set showcmd		"显示按下的按键

"-------------------------------------
" text searching/matching
"-------------------------------------
set hlsearch		"搜索(/)高亮
exec "nohlsearch"		"进入文件后删除搜索历史
set incsearch		"实时显示搜索高亮
set ignorecase		"搜索忽略大小写
set smartcase		"开启智能大小写
noremap _ nzz		"下一个搜索匹配
noremap + Nzz		"上一个搜索匹配

"-------------------------------------
" text formatting/layout
"-------------------------------------
set tabstop=4		"tab键宽度
set shiftwidth=4
set softtabstop=4
set expandtab		"实际使用tab字符，不要用空格字符替换
set autoindent		"继承前一行的缩进方式
set tw=0
set indentexpr=
set backspace=indent,eol,start
set foldmethod=indent
set foldlevel=99
set list		"显示行尾是否有空格，用$表示结束
syntax on		"语法高亮

colorscheme slate		"主题设置
```

*ps：leader键就相当于windows中的super键*

## 无注释版

```vim
"------------------------------
"设置leader映射,空格为leader键
"------------------------------
let mapleader=" "
noremap <LEADER><CR> :nohlsearch<CR>
noremap <LEADER>S :split<CR>
noremap <LEADER>V :vsplit<CR>
map <LEADER>l <C-w>l
map <LEADER>k <C-w>k
map <LEADER>h <C-w>h
map <LEADER>j <C-w>j
" map <LEADER><LEADER> <Esc>/<++><CR>:nohlsearch<CR>c4i

"----------------------------
"普通键位映射
"----------------------------
map S :w<CR>
map Q :q<CR>
noremap H 5h
noremap J 5j
noremap K 5k
noremap L 5l
map <up> :res -5<CR>
map <down> :res +5<CR>
map <left> :vertical resize-5<CR>
map <right> :vertical resize+5<CR>
map th :-tabnext<CR>
map tl :+tabnext<CR>
map tu :tabe<CR>j
map sv <C-w>t<C-w>H
map sj <C-w>t<C-w>K
map <LEADER>sc :set spell!<CR>
map <C-y> <C-r>
map <C-c> "+y       "设置复制到系统剪切板上

"----------------------------
"全局的部分配置
"----------------------------
set enc=utf8
set fencs=utf8,gbk,gb2312,gb1803
set nocompatible
filetype on
filetype indent on
filetype plugin on
filetype plugin indent on
" set mouse=a       " 设置是否可用鼠标
set encoding=utf-8
let &t_ut=''
set scrolloff=5
set autochdir
noremap <leader>w :%s/\s\+$//<cr>:let @/=''<CR>
au BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | exe "normal! g'\"" | endif

"------------------------------
" Vim UI
"------------------------------
set number
set relativenumber
set cursorline
set wrap
set wildmenu
set showcmd

"-------------------------------------
" text searching/matching
"-------------------------------------
set hlsearch
exec "nohlsearch"
set incsearch
set ignorecase
set smartcase
noremap _ nzz
noremap + Nzz

"-------------------------------------
" text formatting/layout
"-------------------------------------
"set paste  " 设置是否自动注释
set tabstop=4
set shiftwidth=4
set softtabstop=4
set expandtab
set autoindent
set tw=0
set indentexpr=
set backspace=indent,eol,start
set foldmethod=indent
set foldlevel=99
syntax on

" 配色方案
colorscheme monokai
"set background=dark " for the dark version
"set background=light " for the light version


" 设置背景透明度
hi Normal ctermfg=252 ctermbg=none

" =========================================
" 光标设置
" "Mode Settings

"let &t_SI.="\e[5 q" "SI = INSERT mode
"let &t_SR.="\e[4 q" "SR = REPLACE mode
"let &t_EI.="\e[1 q" "EI = NORMAL mode (ELSE)

if has("autocmd")
  au VimEnter,InsertLeave * silent execute '!echo -ne "\e[1 q"' | redraw!
  au InsertEnter,InsertChange *
    \ if v:insertmode == 'i' | 
    \   silent execute '!echo -ne "\e[5 q"' | redraw! |
    \ elseif v:insertmode == 'r' |
    \   silent execute '!echo -ne "\e[3 q"' | redraw! |
    \ endif
  au VimLeave * silent execute '!echo -ne "\e[ q"' | redraw!
endif

"Cursor settings:

"  1 -> blinking block
"  2 -> solid block
"  3 -> blinking underscore
"  4 -> solid underscore
"  5 -> blinking vertical bar
"  6 -> solid vertical bar
"=============================================
```



# 可视模式（Visual）

> 可视模式又可以理解为“块模式”

## 普通可视模式

相当于是鼠标按住拖动的操作（选中）

**进入模式方式：按<kbd>v</kbd>**（小写）

*在这种模式中普通的动作（motion）也是生效的，eg：<kbd>d</kbd>剪切选中内容、<kbd>y</kbd>复制选中内容*

## 可视行模式

顾名思义，一行行选中（效率很高，很快啊🐱‍🏍）

**进入模式方式：按<kbd>V</kbd>**（大写）

## 可视模式下的命令

> 其实上两种模式都可以进入指令模式，这里单独拎出来只因为这个太强了。

**进入方式：在前两种状态下，按<kbd>:</kbd>，然后写入命令**

使用：eg：

```vim
:'<,'>normal Isomething-I-write
```

<font color=#34b>**输入`normal `然后这之后的内容就会转化为我们在普通模式下的指令，你输入的啥，vim就会执行啥。**</font>

*ps：`'<,'>`是我们按下冒号之后自动生成的，之后的内容才是我们想写入的*

## 可视块模式

>  这个模式是最强的。块，其实就是矩形。vim在这个模式下允许你进行矩形区域的选中，这是非常快捷方便的（操作中就能感受到）

**进入模式方式：按下<kbd>Ctrl</kbd>+<kbd>v</kbd>**

*更高级的玩法：*

<font color=brown>在可视块模式下并有选中的情况下，按下任意能进入写入模式的按键（如c），然后写入并退出到普通模式，然后这些行会全部被写入。</font>

# 分屏

## 基本使用

- **先`:`指令模式，然后就是`split`**
- **如果是按`vsplit`就是左右分屏**

<u>**分屏切换的主要按键就是<kbd>Ctrl</kbd>+<kbd>w</kbd>，然后按方向键（hjkl），或者再按一个w就可以转到下一个分屏**</u>

*但是这显然就太麻烦了，我们可以去配置一下按键映射：*

```vim
map <LEADER>l <C-w>l
map <LEADER>k <C-w>k
map <LEADER>h <C-w>h
map <LEADER>j <C-w>j
```

## 上下分屏和左右分屏的切换

> 直接上配置

```vim
map sv <C-w>t<C-w>H
map sj <C-w>t<C-w>K
```

然后按<kbd>s</kbd>+<kbd>v</kbd>就可以改左右分屏了，同时<kbd>s</kbd>+<kbd>j</kbd>可以改上下分屏。

## 打开文件

> 当你在搞一个项目时，肯定是想要多个文件在分屏中展示，然后分别编辑的，这时候就要用edit指令了

`e`指令：（也可以敲`edit`）

```vim
:e ~/myprogram/
```

*于是它就打开了myprogram这个文件夹，<u>并关闭当前窗口</u>，然后你就可以在这个简易的UI中选中你想要打开的文件。*

## 更改分屏大小

> 使用`resize`指令

eg：

```vim
:resize-5
:vertical resize-5
```

第一条指令是让上下分屏减小5个单位（具体是什么单位不知道），第二条就是左右分屏了。(`reszie`可简写为`res`)

当然，这样做也是贼麻烦，我们还是映射一波：

```vim
map <up> :res -5<CR>
map <down> :res +5<CR>
map <left> :vertical resize-5<CR>
map <right> :vertical resize+5<CR>
```

# 使用标签页

> 这个玩意应该熟悉，就是编辑器上面的标签选项

## 创建新的标签

```vim
:tabe [标签名]
```

输入这条指令之后vim就会创建一个新的标签页并覆盖掉原有的标签页(*然后你就会看到第一行上出现了一个简易的标签页展示UI*)

还是照例配置一下，免得得输入指令：

```vim
map tu :tabe<CR>
```

## 移动标签

```vim
:-tabnext
:+tabnext
```

第一条指令是移到左边标签，相应的`+`就是移到右边。

配置：

```vim
map th :-tabnext<CR>
map tl :+tabnext<CR>
```

# vim-plug

> [vim-plug](https://github.com/junegunn/vim-plug)是一个插件管理器，能够下载并管理vim上的插件。当然有人说还有[Vundle.vim](https://github.com/VundleVim/Vundle.vim)，好吧，但是这东西我觉得坑太多。

## 简单安装

**咱们看到官方文档中怎么说：**

- Unix

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

- Windows(PowerShell)

```bash
iwr -useb https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim |`
    ni $HOME/vimfiles/autoload/plug.vim -Force
```

*然后不出所料你会收到以下 报错信息：*

```vim
Failed to connect to raw.githubusercontent.com port 443: Connection refused
```

**可以尝试以下解决方法：**

**第一种：**

*用RO我搭建好的文件链接再运行：*

```bash
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    http://39.98.122.169:3000/plug.vim
```

**第二种：**

1. 打开hosts

```bash
vim /etc/hosts
```

2. 在hosts里面<u>添加</u>下列内容

```vim
199.232.28.133 raw.githubusercontent.com
```



## 简单使用

> 在 vim 配置文件 `~/.vimrc` 或 Neovim 配置文件 `~/.config/nvim/init.vim` 中增加 vim-plug 配置项（就是我之前展示的配置文件）

- 开始部分`call plug#begin()`，括号里面写入的是vim的插件安装到哪里，因为我们安装的vim-plug是在`.vim`中，而`.vim`中有一个`plugged`文件夹就是专门存放vim插件的。
- 结束部分`call plug#end()`。
- 中间列出要安装的插件，都要以`Plug`开头

结果就是这样的：

```vim
call plug#begin('~/.vim/plugged')

Plug 'vim-airline/vim-airline'
Plug 'connorholyday/vim-snazzy'

call plug#end()
```

然后重新打开`.vimrc`，输入命令`:PlugInstall`，就会自动加载安装插件（很快啊🐱‍🏍）

## 相关命令

| 命令                                | 描述                         |
| :---------------------------------- | :--------------------------- |
| `PlugInstall [name ...] [#threads]` | 安装插件                     |
| `PlugUpdate [name ...] [#threads]`  | 安装或更新插件               |
| `PlugClean[!]`                      | 清除不常用目录               |
| `PlugUpgrade`                       | 更新 vim-plug 自身           |
| `PlugStatus`                        | 检查插件状态                 |
| `PlugDiff`                          | 检查上次更新和挂起更新的修改 |
| `PlugSnapshot[!] [output path]`     | 生成插件快照                 |

*每个指令都要加`:`就不用说了吧*

## 插件按需加载

```vim
" NERD tree 在 NERDTreeToggle 第一调用时加载
Plug 'scrooloose/nerdtree', { 'on': 'NERDTreeToggle' }

" 多命令
Plug 'junegunn/vim-github-dashboard', { 'on': ['GHDashboard', 'GHActivity'] }

" 当 clojure 文件打开时加载
Plug 'tpope/vim-fireplace', { 'for': 'clojure' }

" 多文件类型
Plug 'kovisoft/paredit', { 'for': ['clojure', 'scheme'] }

" 两种情况下都可以按需加载
Plug 'junegunn/vader.vim',  { 'on': 'Vader', 'for': 'vader' }

" 插件按需加载时执行代码
Plug 'junegunn/goyo.vim', { 'for': 'markdown' }
autocmd! User goyo.vim echom 'Goyo is now loaded!'
```

`for` 选项通常是不需要的，因为大多数特定文件类型的插件通常代码不是很多

## 推荐的插件

### <font size=5>[NERDTree](https://github.com/preservim/nerdtree)</font>

可以在分屏中显示文件管理（有简易UI），在打开多个文件的情况下是非常有用的。

安装方式：

```vim
Plug 'preservim/nerdtree'
```

添加的配置：

```vim
" 设置NerdTree
map <F3> :NERDTreeMirror<CR>
map <F3> :NERDTreeToggle<CR>
```

### [<font size=5>airline</font>](https://github.com/vim-airline/vim-airline)

安装方式：

```vim
Plug 'vim-airline/vim-airline'
```

可以在vim下方显示状态栏的插件

### [<font size=5>vim-snazzy</font>](https://github.com/connorholyday/vim-snazzy)

单纯为了美化，设置一个新的主题snazzy

安装方式：

```vim
Plug 'connorholyday/vim-snazzy'
```

这时就可以将之前的slate主题换成snazzy了，还可以设置背景透明（可能因为终端不同而无法显示：

```vim
"snazzy主题的配置项
let g:SnazzyTransparent = 1
```

### [<font size=5>vim-rainbow</font>](https://github.com/frazrepo/vim-rainbow)

可以将多级的大括号进行颜色区分

安装方式：

```vim
Plug 'frazrepo/vim-rainbow'
```

添加的配置：

```vim
"rainbow的配置项
let g:rainbow_active = 1
```

### [<font size=5>auto-pairs</font>](https://github.com/jiangmiao/auto-pairs)

顾名思义，就是在你写了或删了一个括号时自动同步另一个括号。

安装方式：

```vim
Plug 'jiangmiao/auto-pairs'
```

添加的配置：

```vim
"auto-pairs的配置项
au Filetype FILETYPE let b:AutoPairs = {"(": ")"}
au FileType php      let b:AutoPairs = AutoPairsDefine({'<?' : '?>', '<?php': '?>'})
```

### <font size=5>[NerdCommenter](https://github.com/preservim/nerdcommenter)</font>

> 所有程序员编写的代码都会产生难以调试的问题，导致他们决定注释掉或撤消代码，NERD Commenter的用处就来了，选择代码，点击Leader+cc，你的代码就会被注释掉；点击Leader+cn，你的代码将被取消注释。对于大多数文件类型，NERD Commenter都能使用正确的注释字符。

安装方式：

```vim
Plug 'preservim/nerdcommenter'
```

### <font size=5>[tagbar](https://github.com/preservim/tagbar)</font>

在右边显示函数列表

安装方式：

```vim
Plug 'preservim/tagbar'
```

添加配置：

```vim
nmap <F8> :TagbarToggle<CR>
```

*然后你就可以直接按<kbd>F8</kbd>来显示函数列表了。*

### <font size=5>[MarkdownPreview](https://github.com/iamcco/markdown-preview.nvim)</font>

还是顾名思义，是可以在vim正在编辑的情况下实时查看md文档（在web浏览器中）的插件

安装方式：

```vim
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }, 'for': ['markdown', 'vim-plug']}
```

**命令：**

```vim
MarkdownPreview
" 在打开 markdown 文件后，使用该命令可以打开预览窗口

MarkdownPreviewStop
" 关闭 markdown 预览窗口，并停止开启的服务进程
```

添加配置：

```vim
let g:mkdp_browser = 'chromium'
autocmd Filetype markdown noremap ,v :MarkdownPreview<CR>
autocmd Filetype markdown noremap ,vs :MarkdownPreviewStop<CR>
```

- 上面的第一句是将预览的默认浏览器设置为 chromium (可选，如果不指定则使用系统的默认浏览器打开)
- 后两句开始的 `autocmd Filetype markdown` 声明只有在文件类型为 markdown 时这两个快捷键才会生效
- 第二句是将 `:MarkdownPreview` 命令绑定到 `,v` 组合键上
- 第三句是将 `:MarkdownPreviewStop` 绑定到 `,vs` 组合键上

**继续配置相应常用样式快捷键**：

```vim
autocmd Filetype markdown noremap ,b i****<Esc>hi
autocmd Filetype markdown noremap ,i i**<Esc>i
```



### <font size=5>[leaderF](https://github.com/Yggdroot/LeaderF)</font>

LeaderF是一个用Python写的vim插件，可以在成千上万数十万个文件中，通过模糊查找的方式，快速找到目标文件。

安装：

```vim
Plug 'Yggdroot/LeaderF', { 'do': ':LeaderfInstallCExtension' }
```

常用指令：

| 指令             | 功能                                            |
| ---------------- | ----------------------------------------------- |
| :LeaderfFile     | 搜索当前目录下的文件                            |
| :LeaderfMru      | 搜索最近使用过的文件( most recently used files) |
| :LeaderfLine     | 搜索当前文件中有的某个单词                      |
| :LeaderfFunction | 搜索当前文件的函数                              |
| :LeaderfBuffer   | 搜索当前的Buffer                                |



### <font size=5>[coc.nvim](https://github.com/neoclide/coc.nvim)</font>

作为IDE，必不可少的就是代码智能补全，coc.nvim就是<u>坑比较少</u>的一种智能补全插件。

安装(得先安装nodejs)：

```bash
curl -sL install-node.now.sh/lts | bash
```

```vim
Plug 'neoclide/coc.nvim', {'branch': 'release'}
```

添加配置：

**<u>coc.nvim只是一个平台</u>，它能够允许你安装各种语言插件，达到不同语言的补全效果。因此我们只有安装了对应的语言插件才能实现补全。以C/C++为例：**

通过在vim内的命令模式输入:CocConfig来配置coc.nvim的配置文件coc-settings.json

```vim
{"languageserver": {    "clangd": {      "command": "clangd",      "rootPatterns": ["compile_flags.txt", "compile_commands.json"],      "filetypes": ["c", "cc", "cpp", "c++", "objc", "objcpp"]    }  }}
```

*Ps: 这种配置模式下，coc主要是依赖clangd进行代码自动化补全，所以需要事先安装好clangd。*

> 对更多其他语言的支持可以参考: https://github.com/neoclide/coc.nvim/wiki/Language-servers#supported-features

当然你要是不想要这么麻烦，coc.nvim本身作为一个平台，其自身就包含各种扩展，可通过各种指令来安装高阶的扩展插件。（之前的C补全就可以用扩展插件实现）

| 命令       | 命令名               |
| ---------- | -------------------- |
| 安装命令   | :CocInstall 插件名   |
| 移除命令   | :CocUninstall 插件名 |
| 查看已安装 | :CocList extensions  |
| 更新命令   | :CocUpdate           |

> coc的扩展插件可参考: https://github.com/neoclide/coc.nvim

# 杂谈

## [figlet](https://github.com/cmatsuoka/figlet)

> 当我们想要生成大号字符的ascii图形时，可以用[figlet](https://github.com/cmatsuoka/figlet)帮助我们快速生成。(因为是用C编译的，得装gcc编译器)

### 安装

```bash
wget ftp://ftp.figlet.org/pub/figlet/program/unix/figlet-2.2.5.tar.gz
x figlet-2.2.5.tar.gz
cd figlet-2.2.5
make && make install
```

### 使用

```bash
figlet vim
```

结果

```bash
       _
__   _(_)_ __ ___
\ \ / / | '_ ` _ \
 \ V /| | | | | | |
  \_/ |_|_| |_| |_|

```

### 自定义字体

```bash
figlet -f smslant vim
```

结果

```bash
       _
 _  __(_)_ _
| |/ / /  ' \
|___/_/_/_/_/

```

## 文件类型转换

> 我们可以利用vim来将当前文件来转化为`html`或者是`pdf`

转html：

```vim
:%TOhtml
```
