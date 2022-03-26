---
title: Webpack入门
date: 2022-03-10 17:07:06
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/GXilsYQH6ZcJF4t.png
banner_img: https://s2.loli.net/2022/03/26/pj1OSTbiahoCAIJ.png
tags:
- nodejs
categoris:
- 前端
---


# Webpack

## 前置准备

1. 初始化一个npm项目

  ```shell
  npm init -y
  ```

2. 安装webpack及其命令行工具(作为开发依赖)

  ```shell
  npm i webpack webpack-cli -D
  ```

3. 创建一个index.js入口文件

  ```shell
  touch ./src/index.js
  ```

## webpack能干啥

1. 我们可以在index.js中写ES6代码

2. 支持ES6的模块化

3. 压缩代码

## 使用

1. 创建一个配置文件，让webpack开始工作

  ```shell
  touch ./webpack.config.js
  ```

2. 在这个配置文件(webpack.config.js)中写入配置

  ```js
  // CommonJS 语法
  const path = require('path');
  module.exports = {
    // 设置模式
    mode: 'development',
    // 指定入口
    entry: path.join(__dirname, 'src', 'index.js'),
    // 指定打包目的地址
    output: {
      path: path.join(__dirname, 'dist'),
      filename: 'bundle.js'
    }
  }
  ```

  *记住：只能用CommonJS语法，不能用ES6模块化语法(你可以理解为配置文件不是运行文件，环境为nodeJS)*

3. 编写打包脚本

  在`package.json`中：

  ```json
  "script": {
    "build": "webpack"
  }
  ```

  <u>这里的理解就是`webpack`指令会默认读取根目录下的一个`webpack.config.js`文件，然后运行里面的配置</u>

4. 运行咱们的脚本

  ```shell
  npm run build
  ```

## 使用`HtmlWebpackPlugin`

> 使用场景：我们可能需要在打包之后出现一个index.html(这个页面作为应用的主页面，当然我们需要先在项目中已经写好了这个index.html)，在这个index.html中引入我们写的js代码

1. 安装插件

  ```shell
  npm i html-webpack-plugin -D
  ```

2. 编写index.html

  ```shell
  nvim index.html
  ```

3. 编写配置文件(webpack.config.js)

  ```js
  const path = require('path');
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  module.exports = {
    // 设置模式
    mode: 'development',
    // 指定入口
    entry: path.join(__dirname, 'src', 'index.js'),
    // 指定打包目的地址
    output: {
      path: path.join(__dirname, 'dist'),
      filename: 'bundle.js'
    },
    // 添加插件，并配置这些插件
    plugins: [
      new HtmlWebpackPlugin({
        // 告诉HtmlWebpackPlugin插件html模版在哪
        template: path.join(__dirname, 'src', 'index.html'),
        filename: 'index.html'
      })
    ]
  }
  ```


## 使用webpack-dev-server

> 使用场景：我们只要更改了源代码，就需要敲命令来重新打包，webpack不能自动化热更新

> webpack-dev-server就是用来热更新的!


1. 安装webpack-dev-server

  ```shell
  npm i webpack-dev-server -D
  ```

2. 配置webpack-dev-server

  ```js
  devServer: {
    port: 8000,
    static: path.join(__dirname, 'dist')
  }
  ```

  和plugins在同级目录下

3. 配置一个脚本

  ```json
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server"
  }
  ```

4. 运行脚本命令

  ```shell
  npm run dev
  ```

### 注意点

<u>需要注意的是，webpack-dev-server不会生成dis文件（虽然我们配置了），它是存在于内存的，所以我们要想打包我们的项目，还是要`npm run build`</u>

## 使用babel

> babel是干嘛的：前端一个最重要的任务，解决兼容问题!我们必须保证我们写的ES6代码可以运行于所有的浏览器上，即使它只支持ES5

> 于是babel帮我们将ES6代码转换为ES5

1. 安装babel一系列依赖

  ```shell
  npm i @babel/core @babel/preset-env babel-loader -D
  ```

2. 为babel创建一个配置文件`.babelrc`

  ```shell
  touch .babelrc
  ```

3. 写入配置

  ```json
  {
    // 预设：babel一系列插件的集合
    "presets": ["@babel/preset-env"]
  }
  ```

4. 设置一下webpack的配置

  ```json
  // 解析模块的规则
  module: {
    rules: [
      {
        // 匹配所有的js文件，将其使用babel加载
        test: /\.js$/,
        loader: 'babel-loader',
        // 引入src目录，排除node_modules
        include: path.join(__dirname, 'src'),
        exclude: /node_modules/
      }
    ]
  }
  ```

  *就在根配置下*

5. 最后跑一下试试

  ```shell
  npm run build
  ```

