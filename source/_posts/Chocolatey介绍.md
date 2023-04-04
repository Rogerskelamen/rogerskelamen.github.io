---
title: Chocolatey介绍
date: 2021-11-30 20:45:29
index_img: https://s2.loli.net/2022/03/26/K4aVcsDjlE7rTP5.png
banner_img: https://s2.loli.net/2022/03/26/2Y1sbw8rpZhJc6T.png
categories:
- Windows
- 工具
tags:
- package
- geek
---


# Chocolatey

> [Chocolatey](https://link.zhihu.com/?target=https%3A//chocolatey.org/)是Windows平台上的包管理器，通过它可以集中安装、管理、更新各种各样的软件。

1. 是和apt-get，brew差不都的一个东西。
2. 可以**一条命令更新全部软件**，特别适合治疗自己的更新强迫症（尤其是遇上一些不能自动检查更新的软件时）。
3. 特别适合管理一些小众、轻量的开源软件。(*比如Oh-My-Posh*)

## Installation

可以参考[官方的文档](https://chocolatey.org/install)，然后可以完成安装：

1. 使用管理员权限打开powershell（或者是你的Windows Terminal）

2. run `Set-ExecutionPolicy AllSigned` 和 `Set-ExecutionPolicy Bypass -Scope Process`

3. 使用`Get-ExecutionPolicy`查看[ExecutionPolicy](https://go.microsoft.com/fwlink/?LinkID=135170)是否是`AllSigned`

4. run:

   ```powershell
   Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
   ```

5. 使用`choco`来查看是否安装上了

## Usage

- `choco list/search [应用名]`

  搜索应用

- `choco info [应用名]`

  列出应用的详细信息

- `choco install [软件包名]`

  显然就是来安装软件的

  *`-y`选项来默认确认安装*

- `choco list/search -l`

  查看本地安装的所有应用

- `choco list/search [应用名] --by-id-only `

  只返回id中含有关键字的应用

- `choco uninstall [应用名]`

  自动卸载应用（一个或多个）

- `choco outdated`

  **检查**一下哪些应用需要更新

- `choco update all`

  更新所有的软件

  *当然你也可以直接用`choco upgrade [应用名]`更新某一个软件*

## How Strong

- 安装python？

  `choco install python -y`

- 安装jdk8？

  `choco install jdk8 -y`

- 安装gcc?

  `choco install mingw -y`

- 安装node？

  `choco install node -y`

- 安装yarn？

  `choco install yarn -y`

## Necessary software

> 除了上面所说的这些软件，choco还提供了很多的东西可以整

- [winfetch](https://github.com/kiedtl/winfetch)

  对比`neofetch`的展示命令

- [Sudo](https://github.com/janhebnes/chocolatey-packages)

  类似于Linux中的`sudo`命令

- [Bat](https://github.com/sharkdp/bat)

  一个很好的代替`cat`的指令

- [grep](https://www.gnu.org/software/grep/)

  可以在windows平台使用`grep`了
