---
title: Vim插件使用记录
date: 2021-08-11 15:17:51
author: Rogers Kelamen
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/cocPlug.png
tags:
- geek
categories:
- Vim
---

# Vim插件使用记录

> 顺便说一句，可以去这个网站[vimawesome](https://vimawesome.com/)看看你想要的插件。

## [vim-visual-multi](https://github.com/mg979/vim-visual-multi)

> 首先说明一下，我是通过up主"theCW"得知的这个插件——多光标的更改文本，这是他的[b站视频](https://www.bilibili.com/video/BV1Gy4y1q7Co?from=search&seid=4548851410038160589)，但是他的视频中的`visual-multi`版本是较早版本的了。

### Installation

```vim
Plug 'mg979/vim-visual-multi', {'branch': 'master'}
```

### Usage

开启`V-M`模式（这个插件的主要功能就是开启这个模式）：press <kbd>C-n</kbd>

*这个时候光标就选中了当前的词，然后再按<kbd>C-n</kbd>就开始匹配下一个词*

然后<kbd>n</kbd>选择下一个，<kbd>N</kbd>返回上一个选中，<kbd>q</kbd>跳过当前选择下一个。（*这个和up主说的不太一样，因为版本更新*）

#### Notice

<u>需要注意的一点是，当你使用`V-M`模式的时候，你在做完修改之后按<kbd>Esc</kbd>之后并没有退出该模式，你还需按一次退出才能退出`V-M`模式</u>

`visual-multi`同时还提供了<kbd>Ctrl+Up</kbd>和<kbd>Ctrl+Down</kbd>的组合来使你可以一次性选中多行中的一个位置，*我个人觉得并没什么用，因为选择块模式(<kbd>C-v</kbd>)已经满足我们对这样的需求*


## [vim-airline](https://github.com/vim-airline/vim-airline)

### Installation

```vim
Plug 'vim-airline/vim-airline'
```

### Usage

其实很简单，你会看到vim下方出现了一个彩色的状态条

里面显示了诸如**模式，文件路径，文件类型，行数列数**等信息

### Configuration

首先你肯定想要美化一下airline

- 更改主题(colorscheme)

    首先你可以安装一个vim-airline-themes

    ```vim
    Plug 'vim-airline/vim-airline-themes'
    ```

    然后你就可以设置一个自己喜欢的主题, 比如`let g:airline_theme='atomic'`

- 设置airline下方的符号

    你肯定会想要一个有多个符号的airline，如果你安装了nerdfont就可以这样配置

    ```vim
    " airline的所有符号设置
    if !exists('g:airline_symbols')
        let g:airline_symbols={}
    endif
    " unicode symbols
    let g:airline_left_sep = ''
    let g:airline_left_alt_sep = ''
    let g:airline_right_sep = ''
    let g:airline_right_alt_sep = ''
    let g:airline_symbols.colnr = ' :'
    let g:airline_symbols.readonly = ''
    let g:airline_symbols.linenr = ' :'
    let g:airline_symbols.maxlinenr = '☰ '
    let g:airline_symbols.branch = ''
    let g:airline_symbols.dirty='⚡'
    ```

- 设置airline的section配置

    ```vim
    " =============================
    " ==== airline section配置 ====
    " =============================
    " let g:airline_section_c = airline#section#create(['tagbar'])
    let g:airline_section_b = '%{strftime("%d/%m %H:%M")}'
    " let g:airline_section_x = '%{expand("%")}'  " 显示文件名
    " let g:airline_section_y = airline#section#create(['%{strftime("%D")}'])
    let g:airline_section_z = airline#section#create(['%{strftime("%m/%d %H:%M ")}', 'linenr', 'maxlinenr'])
    " let g:airline#extensions#tabline#enabled = 1
    ```
    
*这个东西主要配置你的airline显示什么内容*


## [NERDTree](https://github.com/preservim/nerdtree)

> 不得不说nerdtree这个玩意真的是个大头，你可以将其理解成VS Code里面的文件管理视图。有很多配合`NERDTree`使用的插件。

### Installation

```vim
Plug 'preservim/nerdtree'
```

### Usage

可以通过绑定的键位来调出`NERDTree`，比如我的就是<kbd>Ctrl+b</kbd>(<del>和VS Code一样</del>)，然后按出之后就可以调出`NERDTree`了。

![invoke nerdtree](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20210811145254.png)

*还是不错的。*

### Plugin in Plugin

有很多的插件可以配合`NERDTree`比如`vim-devicons`,`vim-nerdtree-syntax-highlight`,`nerdtree-git-plugin`

*第一个就是我可以显示图标的必装插件*，这些插件都可以到`NERDTree`的主页找到

### Configuration

8说了，直接上配置代码：

```vim
" =========================
" ===== 设置NerdTree ======
" =========================
map <C-b> :NERDTreeMirror<CR>
map <C-b> :NERDTreeToggle<CR>
:let g:NERDTreeWinSize=25
autocmd BufEnter * if tabpagenr('$') == 1 && winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif " 如果是最后一个窗口就关闭nerdTree
autocmd BufEnter * if winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif  " 如果是最后一个标签就关闭nerdTree
" 设置NERDTree和nerdtree-git联动
let g:NERDTreeGitStatusUseNerdFonts = 1
let g:NERDTreeGitStatusShowIgnored = 1
let g:NERDTreeGitStatusConcealBrackets = 0
let g:NERDTreeGitStatusIndicatorMapCustom = {
    \ 'Modified'  :'✹',
    \ 'Staged'    :'✚',
    \ 'Untracked' :'✭',
    \ 'Renamed'   :'➜',
    \ 'Unmerged'  :'═',
    \ 'Deleted'   :'✖',
    \ 'Dirty'     :'✗',
    \ 'Ignored'   :'☒',
    \ 'Clean'     :'✔︎',
    \ 'Unknown'   :'?',
    \ }

    " nerdtree和vim-devicons联动
    let g:webdevicons_conceal_nerdtree_brackets=1
    " 使用nerd-highlight-syntax
    let g:WebDevIconsDisableDefaultFolderSymbolColorFromNERDTreeDir = 1
    let g:WebDevIconsDisableDefaultFileSymbolColorFromNERDTreeFile = 1
```

## [markdown-preview.nvim](https://github.com/iamcco/markdown-preview.nvim)

> 用vim来写`.md`文件就是一种享受对么，所以当然要配备上preview插件。

> 你可能还听说[instant-markdown](https://github.com/instant-markdown/vim-instant-markdown)，也是完成同样的预览功能，而且还是纯用`vimscript`写的，连theCW大佬都是用的这一款。*但是为什么我还要推荐markdown-preview呢，纯粹是我用习惯了懒得换了XD*

### Installation

```vims
Plug 'iamcco/markdown-preview.nvim', { 'do': { -> mkdp#util#install() }, 'for': ['markdown', 'vim-plug']}
```

可以看到因为使用了js，所以次插件依赖于node才能运行。

### Usage

还是老规矩：绑定键位，然后调用

*ps: 配合自己写的`Run`脚本更香哦*

### Plugin in Plugin

推荐安装[vim-table-mode](https://github.com/dhruvasagar/vim-table-mode)

*用来格式化表格的工具插件，同样是绑定键位食用*
