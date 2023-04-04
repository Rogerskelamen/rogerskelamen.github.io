---
title: npm使用
date: 2022-04-07 15:56:28
index_img: https://s2.loli.net/2022/04/07/IVJfLlYM26drkwn.png
author: Rogers Kelamen
tags:
- js
- package
categories:
- 工具
---


# npm #

> 怎么用应该不用解释了

> 就说几个比较用到的指令

1. `npm i [package]`

  在项目中安装某包

2. `npm i [package] -D`

  在开发环境下安装某包

3. `npm i [package] -g`

  全局安装某包

4. `npm list --depth=0 -g`

  列出全局的所有包

## 推荐几个有用的npm包 ##

1. [`nodemon`](https://www.npmjs.com/package/nodemon)

  好用的nodeJS程序热更新功能

2. [`validator`](https://www.npmjs.com/package/validator)

  一个字符串验证器和清理器库

3. [`http-server`](https://www.npmjs.com/package/http-server)

  以服务器环境运行html的工具，相当于vscode里面的`live server`

4. [`cors`](https://www.npmjs.com/package/cors)

  用来实现跨域的方便中间件

  *可配合`express`一同食用：*

  ```js
  app.use(
    cors({
      origin: "*"
    })
  )
  ```

