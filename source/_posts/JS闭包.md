---
title: JS闭包
date: 2022-03-21 19:24:01
index_img: https://s2.loli.net/2022/03/26/upFSjGVQNk8I3ZL.png
author: Rogers Kelamen
tags:
- js
categories:
- 前端
---


# JS 闭包详解 #

> <u>简单来说，在一个函数中定义的函数被return保存到了外部，就会产生闭包</u>

**更为准确的一种说法是：在函数返回值值中包含另一个函数的声明(比如返回了一个对象)就可以形成闭包**

```js
function test() {
  let arr = []
  for (let i = 0; i < 10; i++) {
    arr[i] = function () {
      console.log(i)
    }
  }
  return arr
}

let myArr = test()
for (let j = 0; j < 10; j++) {
  myArr[j]();
}
// 结果是10个10
```

闭包的好处就是我们可以在全局访问一个内部的变量，但是坏处是<u>会造成内存泄漏</u>

## 底层原理 ##

> 说到底，就是js的上下文作用域链

在一个函数被声明时，它会记住当前的环境并将其保存，然后调用的时候又回产生一个作用域，之前声明时的作用域环境被下移。

<u>由于在函数内部的函数被声明时的环境为外层函数的作用域，所以会直接继承所有外层函数的作用域，于是包含了外层函数的变量，外层函数的变量变得可访问</u>

## 立即执行函数 ##

```js
(function (){
  ...
}())
```

只有表达式才能被执行符号`()`执行

被立即执行之后会立即销毁这个函数

**所以，只要是表达式，就能被执行符号<u>立即执行</u>**

```js
!function (){

}()
// 这种形式也是立即执行函数，因为!variable就是一种表达式
```

我们通常用立即执行函数来避免闭包的产生(如果我们不希望闭包的内存泄漏效应)

> 让我们再次看看上面闭包的例子

```js
function test() {
  let arr = []
  for (let i = 0; i < 10; i++) {
    (function (j){
      arr[j] = function () {
        console.log(j)
      }
    }(i))
  }
  return arr
}

let myArr = test()
for (let j = 0; j < 10; j++) {
  myArr[j]();
}
// 这下就变成了正常的0~9
```

<u>简单来说，想避免闭包效应，我们可以构造一个形参为可泄漏变量的立即执行函数，然后应用形参就可以将可泄漏变量销毁</u>
