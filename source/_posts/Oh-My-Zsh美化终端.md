---
title: Oh-My-Zsh美化终端
date: 2020-12-23 19:58:40
author: Rogers Kelamen
tags:
- shell
- geek
categories:
- zsh
index_img: https://s2.loli.net/2022/03/26/1ct9GgOPh5wHxFq.png
banner_img: https://s2.loli.net/2022/03/26/mqW2Jwr4bcoKGRd.png
---

# Oh-My-Zsh美化终端

> 看着Linux的黑白终端（无GUI的mini版）很快就会感到厌烦，然后就想要弃坑，估计很多人第一次接触Linux都是这种感觉（我反正是这样🥱）。所以有一个漂亮的终端美化方案就是我们开始敲命令行的前提：毕竟咱们技术虽然不咋滴，但是逼格还是要起来的o(*￣▽￣*)ブ。

# 引入zsh

> 我这个教程相当于是用[oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh)来简单配置美化终端的，所以最开始的[zsh](https://github.com/zsh-users/zsh)还是要有的。啥，你没听说过[zsh](https://github.com/zsh-users/zsh)？讲真我也不知道zsh能干啥，据说巨佬可以通过复杂的配置来使得zsh成为超强的shell (p≧w≦q) 。但是我这种菜鸟当然不知道怎么配置这么“高级”的shell，不过不用担心，咱们有[oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh)，根据大佬的项目我这种菜鸟也可以简单的配置zsh了。

> 噢对了，你要是想要追求更酷炫的，可以去试试[fish](https://github.com/fish-shell/fish-shell)以及它的配套项目[oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)。fish的酷炫是舍弃了旧版shell脚本语法作为代价，所以你要是想要酷炫而且非常爱折腾值得试一试。

安装必要的软件：

```bash
apt install git-core zsh
```

*就是下一个git和zsh，如无特殊说明以下都是以ubuntu系统为例*

**然后你就在`/etc/shells`中可以看到出现了咱们安装的zsh：**

```bash
cat /etc/shells
```

![image-20201223122514830](https://s2.loli.net/2022/03/26/ChryGn4V6SwvmbD.png)

看到咱们的zsh已经出现了，就可以将zsh设为默认shell了：

```bash
chsh -s /bin/zsh
```

*看看是否生效了，查看当前shell：*

```bash
echo $SHELL
```

**要是没生效<u>还是bash的话</u>，重启一下就好了。**

# 安装Oh-My-Zsh

用官方提供的脚本安装oh-my-zsh：

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

然后不出所料你会连接失败，这时候我们就只能用git从github上把项目拉下来：

```bash
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

当然你要是觉得github还是太慢，就用[gitee上的镜像仓库](https://gitee.com/mirrors/oh-my-zsh)（每天更新一次）：

```bash
git clone https://gitee.com/mirrors/oh-my-zsh.git ~/.oh-my-zsh
```

**拉下来之后记得创建.zshrc配置文件：**

```bash
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

*ps：这时候应该是配置好了会有变化的，要是没变化source一下（或重启）即可*

# 配置Oh-My-Zsh

*首先安装Powerline字体来显示图标：*

```bash
apt install fonts-powerline
```

然后就是重头戏了，打开`.zshrc`配置文件：

```bash
vim ~/.zshrc
```

找到主题配置的那一项，将主题改为`agnoster`（或者也可以是别的，`random`就是每次打开主题都不一样）：

```text
ZSH_THEME="agnoster"
```

出去之后source一下就可以变过来了：

```bash
source ~/.zshrc
```

我们看到agnoster主题会显示主机名和用户名，我们可以在`.oh-my-zsh`的主题文件夹中配置一下去掉：

```bash
vim ~/.oh-my-zsh/themes/agnoster.zsh-theme
```

找到`prompt_context`配置（在`build_prompt`函数中），然后将其注释掉：

![](https://s2.loli.net/2022/03/26/1Qq8UknGF7ZwmfT.png)

*首先你得会用vim的搜索方法。。*

然后再source一波就会生效了：

```bash
source ~/.zshrc
```

# Oh-My-Zsh的使用

## 目录历史

Zsh会自动记住您访问过的最后20个目录。您可以使用`dirs -v`或`d`来按时间顺序列出历史记录。

![](https://s2.loli.net/2022/03/26/ix41p9IQtnhdEGT.png)

然后可以使用`cd +1`转到上一个目录，依此类推，如下图我们还可以直接输入数字进行跳转到对应的目录，目录管理功能非常强大。

![](https://s2.loli.net/2022/03/26/83w5WauQIq6RVgK.png)

**快速进入常用目录**：

```bash
/ -> cd /
~ -> cd ~
.. -> cd ..
... -> cd ../..
.... -> cd ../../..
```

**`take`命令使用：**

```bash
take test_folder # 创建一个文件夹并进入这个文件夹，效果和下面类似
mkdir test_folder && cd test_folder
```

> 当然这些使用都还是zsh的冰山一角，你可以在网上找一些关于zsh的教程来学习。

## 更新

```bash
upgrade_oh_my_zsh
```

## 卸载

```bash
uninstall_oh_my_zsh
```

# 为Zsh安装插件

> oh-my-zsh中本身就有很多的插件，你可以直接在`~/.oh-my-zsh/plugins`目录中看到各种各样的插件（比如pip，vscode，sublime啥的）

![](https://s2.loli.net/2022/03/26/CofaDRKgjexP3iT.png)

## 安装自带插件

**直接去`.zshrc`文件中进行配置，找到`plugins=(git)`这里（默认给你装了git），然后直接进行添加就行：**

![](https://s2.loli.net/2022/03/26/Rx3ZSk7pPgYXm8W.png)

**推荐几个自带的好用的插件**：

### **git**

可以使用git命令的各种缩写，eg：

```bash
git add --all ===> gaa
git commit -m ===> gcmsg
```

查看所有的git命令缩写：

```bash
cat ~/.oh-my-zsh/plugins/git/git.plugin.zsh
```

*ps：其实很多的插件文档都可以在插件目录下找到*

### **extract**

就是解压压缩文件用的，只需要一个`x`就可以解压任意格式的压缩包，再也不用担心`tar`的那些参数记不住了。

使用：

```bash
x filename
```

### **colored-man-pages**

将你输入`man`指令之后的指令文档变得有色彩（*有高亮区分显示*），用于美化

## 安装额外插件

>  当然，有些其他的很好的插件我们可以自定义（custom）安装到`~/.oh-my-zsh/custom/plugins`这个文件夹中，而且很多很好用的插件都是需要另外安装的

**推荐安装插件：**

### [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)

**另一个很棒的插件是shell的语法高亮。除此之外，此插件还能够验证命令的正确性（如果不正确则红色显示）**

安装：

```bash
cd ~/.oh-my-zsh/custom/plugins
git clone https://github.com/zsh-users/zsh-syntax-highlighting
```

然后将zsh-syntax-highlighting配置到`.zshrc`配置文件中（之后安装将不再强调）

### [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions.git)

它根据您的命令历史记录建议命令。按一下<kbd>→</kbd>就可以选中之前输入过的历史命令。

安装：

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions
```

### [autojump](https://github.com/wting/autojump.git)

**只需要按一个`j`就可以快速跳转到目标目录**（之前进入过的目录）

安装：

```bash
git clone https://github.com/wting/autojump.git
```

还需要额外配置一下，进入克隆下来的仓库目录，执行（你得先装python）：

```bash
./install.py
```

最后把以下代码加入到`.zshrc`：

```bash
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh
```

**使用**：

```bash
j dirname		# 注意不是路径名而是文件夹名
```

*ps：这里的`dirname`不用写全也可以（可以只是一部分），tql！😈*

删除无效路径：

```bash
j --purge 无效路径
```

## 个人的插件目录

```text
plugins=(
        git
        extract
        colored-man-pages
        autojump
        zsh-syntax-highlighting
        zsh-autosuggestions
        )
```

