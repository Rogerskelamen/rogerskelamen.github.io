---
title: pacman使用记录
date: 2022-02-13 17:16:28
index_img: https://s2.loli.net/2022/03/26/5jJCSEHNDR1kF87.png
banner_img: https://s2.loli.net/2022/03/26/cTHzde5ol3ZEVKs.png
tags:
- geek
categories:
- Manjaro
---


# pacman使用

## `-S`(Synchronize)

- 安装

  ```sh
  sudo pacman -S [package]
  ```

- 模糊查询所有软件包

  ```sh
  sudo pacman -Ss [word]
  ```

- 查询软件包信息

  ```sh
  sudo pacman -Si [package]
  ```

- 更新软件源

  ```sh
  sudo pacman -Syy
  ```

- 更新软件源并更新所有包

  ```sh
  sudo pacman -Syyu
  ```

- 删除所有的安装包缓存

  ```sh
  sudo pacman -Sc
  ```

## `-R`(Remove)

- 删除包的全局配置文件和所有依赖包

  ```sh
  sudo pacman -Rns [package]
  ```

## `-Q`(Query)

- 查询可更新的包

  ```sh
  sudo pacman -Qu
  ```

- 查询自己安装的包(非自动安装)

  ```sh
  sudo pacman -Qe
  ```

- 删除孤包

  ```sh
  sudo pacman -R $(pacman -Qdtq)
  ```

