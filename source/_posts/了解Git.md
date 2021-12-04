---
title: 了解Git
date: 2021-12-04 12:50:32
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211204125421.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20211204125515.png
tags:
- git
categories:
- 后端
---


# 基本概念

- origin

    一般我们的代码提交(commmit)之后我们可能会想要上传到远程仓库中以便我们或其他人在另一台主机上进行代码修改。而这个远程按照规定默认叫做origin

## 使用gitignore

在`.gitignore`中写上你不想要git添加的文件名

书写注意点：**你不需要写上相对路径的`./`，直接写文件或者目录名就好**

### 保留你想要的文件

> 有时候你想要ignore所有的文件(即写上`*`)，但是想要保留的文件也被ignore了

通过使用`!filename`，即可对特定文件或目录不进行ignore

*记住：`.gitignore`是一行行顺序执行的*

## 停止追踪文件

使用`git rm --cache filename`

## 使用branch

> 总所周知，分支是git的一项有力功能，有了branch我们可以同时进行多个版本的开发。

### 开启分支

`git checkout -b branchname`

这条命令其实是创建`branchname`然后转到这个分支上，只想要创建分支使用`git branch branchname`

*记住每次进入branch都是`checkout`指令，快捷指令为`gco`*

### 合并分支

> *注意，你需要在主分支下(一般是master)*

`git merge branchname`

*当然，你也可以在自己的分支下合并主分支（master），使用`git merge master`*

### 删除分支

`git branch -d branchname`

*force delete use `-D`*

### 上传本地分支到远程

`git push -u origin branchname`

这个`-u`参数是`--set-upstream`的缩写

*这种方法其实和最开始`git init`之后将本地的master上传到远程仓库是一个原理*

<u>需要注意的是，使用`git branch -a`查看远程分支时就会看到之前没有的branchname</u>

### 分支重命名

使用指令`git branch -m <name>`，简写：`gb -m <name>`

### 设置默认初始化仓库的分支

> 一般来说，你要是直接`git init`没有指定branchname，那么git会提示你:
```bash
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint: 	git config --global init.defaultBranch <name>
hint:
```

这时候我们就可以用系统给我们的提示指定每次初始化的分支名
`git config --global init.defaultBranch master`

*然后你的`.gitconfig`文件中会出现两行：*

```text
[init]
	defaultBranch = master
```

## 远程(origin)

> 很多时候我们都没搞清楚本地仓库和远程仓库的区别，其中一个最大的区别就是：你的本地仓库的版本控制是你一个人来管理，但是远程仓库就是很多人甚至一个团队来管理版本控制。

每次我们将一个本地仓库推送到一个刚建好的未初始化的远程仓库时，我们都需要提供以下信息：

- 远程仓库地址(https://...)

- 远程仓库在本地的命名(origin)

- 此次推送在远程仓库创建的分支名(master)

**所以每次我们推送空的远程仓库的命令为以下两行：**

```bash
git remote add origin [url]
git push -u origin [branchname]
```

### 修改远程仓库地址

首先想到的就是，修改操作永远都可以分解为删除然后重建。所以我们可以先删仓库，然后加上远程仓库。

```bash
git remote rm origin
git remote add origin [url]
```
