---
title: Vim踩坑记录
date: 2021-08-15 10:42:16
updated: 2021-12-04 20:32:00
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/c4MLy7XxoNkTQ8C.png
categories:
- Vim
tags:
- geek
---


# 前言

> **使用Vim的历史就是人类不断折腾的赞歌**

> Fuck the Vim

## airline-weather

8月7日，weather的icon还是不尽人意，不是nerd font的错误(*每一个nerd font的weather图标都很小*)，应该直接从unicode编码的图形入手

可能解决办法：<u>去windows系统中把unicode图标敲出来，然后写到文件里面直接复制到Ubuntu</u>

---

8月8日，上述方法可行，但是效果不太理想。**现在又出现了新的问题，无法通过设置地理位置来更改weather**

可能解决的办法：<u>提交issue，看作者什么时候回我:(</u>

---

8月11日，*我估计那个作者是不会回我了*。我看了一下项目日期，<u>是2016年的项目</u>，我麻了

*暂时放弃了这个插件(我重新安装之后居然还报出`API error`的错误，服了)，看什么时候作者想起来这个项目了吧*

---

9月5日，我提交了那个issue有人回我了(*应该是一个中国老哥*)，他给的方法暂时能解决刷新问题(*通过强制删除`.weather`文件*)。**不过显然治标不治本，我无法通过设置时间让vim自动给帮我刷新weather**。<u>在这里还是感谢一下帮忙的那位老哥[Freed-Wu](https://github.com/Freed-Wu)</u>

*我累了，毁灭吧*

## vim-visual-multi

8月8日，使用vim-visual-multi有时候会出现错误，在vim中有报错显示，极大原因是和`coc`的配合出现了问题(*在`.html`代码中尤为明显*)。如果出现报错，使用<kbd>Ctrl+n</kbd>能够多选，但是操作会有极大问题。

可能解决的办法：<u>暂时没找到合适的解决方法。但是听说是因为版本的问题，新版本vim和neovim是没有这样的bug的，可以选择移植</u>

---

8月9日，重新编译Vim的话会出现**两种版本的Vim共存的情况**，一个在`/usr/bin/vim`中，也就系统的vim，一个是在`/usr/local/bin/vim`中，也就是自己编译安装的vim。*(麻了，真让人头疼)*新安装的没有了问题(8.2版本)，系统的是8.1版本，看啥时候Ubuntu软件源能够给我面子更新一下自家的vim了:(

---

8月15日，好消息是我不用等apt软件源主动更新vim了。我可以直接用ppa来安装*最近的*vim

```bash
sudo add-apt-repository ppa:jonathonf/vim
```

害，这就好多了，然后更新软件源然后安装就好了，问题得到了解决。(*[主要参考](https://www.codenong.com/f-vim-8-release-install/)*)

<font color=#34b>当然你也不能要求别人ppa和github更新的速度一样快，相差一个月以内就行了</font>


## 浏览器运行html文件

9月5日，我改用neovim和mac的组合了，markdown插件换成了`InstantMarkdown`但是发现使用之后html就无法用谷歌打开了(*当然是用`md_browser`打开的*)，研究了半天找到了解决办法

解决办法：<u>使用`!open %`就能用默认浏览器打开html文件了</u>


## 使用coc-java运行报错

9月11日，The error is: `Error: The "java" server crashed 5 times in the last 3 minutes. The server will not be restarted`

这里主要是jdt lang Server 没有好好地运行(*我是用内部自动安装的，所以安装出了问题*)

解决办法：<u>直接去[eclipse官网](https://download.eclipse.org/jdtls/milestones/0.57.0/)去下载这个server，下载好之后手动安装到`~/.config/coc/extensions/coc-java-data/server`</u>

[问题issue原地址](https://github.com/neoclide/coc-java/issues/99)

## 设置md文件outline预览

9月29日，现在我因为nvim换到了Mac电脑上了，所以之前设置的md文件的导航视窗预览没有了。

解决办法：<u>找到了一个[掘金的帖子](https://juejin.cn/post/6844903441630756871)，发现他采用的`tagbar`功能扩展还不错。但是我又发现他是从github上的[`tagbar#70`](https://github.com/preservim/tagbar/issues/70)，于是找到了一个更好的方法，不仅显示了大纲还可以显示图片地址和链接</u>

## fzf.vim

9月30日，问题描述：**`fzf.vim`在使用时，输入`:Files`报错**(*反正就是很诡异*)

解决办法：<u>我找到了[issue#439](https://github.com/junegunn/fzf.vim/issues/439)，这里面说可以更改`fzf`的目录文件，即更改`dir`，于是我将`Plug 'junegunn/fzf', { 'dir': '~/.local/share/fzf' }`添加到了我的init.vim中。</u>


## Neovim on Windows

> 最近在Windows上开始折腾上了Neovim（不得不说用起来意外地舒服），当然是在Windows Terminal上玩的

> 首先需要注意的是使用Windows的话推荐使用Windows Terminal来打开powershell来编辑，同时推荐使用Chocolatey来管理你的neovim的软件包

12月3日，问题描述：**在安装完Neovim后，输入指令`checkhealth`，结果是python3的依赖没有导入，对nvim会提示`fail to import neovim`，这时候就需要使用`pip`来安装`neovim`的python依赖了: `pip install neovim`，结果发现了bug。**

**问题在于：你安装了neovim依赖之后还是会报错!**

问题查明和解决方法：<u>你使用了较新版本的python，比如我就是用的`python 10.0.0`，只需要将python@10卸载之后安装python@9就好了</u>

### 其他关于`checkhealth`问题

1. 首先就是需要安装关于`node.js`的依赖：`npm install neovim -g`

2. go和rua的依赖，你可以通过查看health详情来看一下到底怎么操作

