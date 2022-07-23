---
title: JS使用注意点
date: 2022-04-08 19:49:01
updated: 2022-04-11 22:20:00
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/04/08/XmCs8Rbd1ngaeI4.png
tags:
- js
categories:
- 前端
---


# JS使用技巧

## `map`,`filter`,`forEach`

> 我们想要更改某个数组的值的话会采取遍历的方式，但是这几种方式那种才最为适合当前情况？

eg:

```js
const arr = [-1, -4, 0, 3, 6]

// 将每个元素乘二
const mapArr = arr.map(item => item * 2)

// 筛选出为偶数的元素
const filterArr = arr.filter(item => item % 2 === 0)

// 这里看上去是将mapArr还原为arr，实际mapArr值没有变化
mapArr.forEach(item => {
  item = item / 2
})
```

*可以看到mapArr并没有修改，那`forEach`只关心元素值吗*

<u>其实使用`forEach`方法，无法修改元素，但是可以修改元素引用下的属性值(因为元素的指针确实没有改变)</u>

```js
const objArr = [
  { a: 1 },
  { a: 2 },
  { a: 3 }
]

objArr.forEach(item => {
  item.a = 0
})

// 可以看到objArr的所有属性a的值都被修改了
console.log(objArr);
```

<u>综上所述，最好还是采用`map`来更改数组，而如果是遍历来查看的话可以考虑`forEach`</u>

## 箭头函数

> 箭头函数咱们都知道，一对括号一个等号一个大于号一对大括号，ES6用来简化函数声明的新特性

<u>但是不建议在较为复杂的数据处理逻辑中使用箭头函数</u>

咱们看看MDN官方的解释：

> 箭头函数表达式的语法比函数表达式更简洁，并且没有自己的this，arguments，super或new.target。箭头函数表达式更适用于那些本来需要匿名函数的地方，并且它不能用作构造函数。

也就是说，如果我们在一个对象的回调方法中使用了箭头函数，this指代的并不是对象本身，而是全局的对象(如`document`)

eg.

```js
$('.option').click(function(e) {
  $options.removeClass('active')
  console.log(this) // 这里的this才是对象本身
  const optionDom = e.target
  $(optionDom).addClass('active')
})
```

<u>所以我们最好在有this等逻辑比较复杂的代码中使用原生，而不是使用箭头函数</u>

## `var`不`var`?

首先看一下这个代码：

```js
var a = 1;
b = 0;
```

这两行哪个是对的？

***答案是都正确***

如果我们正常的使用`var`，那么就是简单的申明变量语句；但是我们不加上`var`，就相当于是给全局变量window加了一个属性和值

但是我们肯定不想每次声明一个变量`window`都多一个属性，所以最好还是用`var`

> How about `let`, `const`?

只需要记住一句话就行：<u>`let`用于基本类型，`const`用于引用类型(*确保指针值不变*)</u>

## 原生还是`jQuery`?

> 这里其实并不是要比较什么时候用原生好，什么时候用jQuery好。我只是想列出我每次使用难以记住的几个常用操作(*总是原生和jQuery用法傻傻分不清*)

1. 设置html的class

- jQuery

  ```js
  $element.removeClass('disactive')
  $element.addClass('active')
  $element.toggleClass('active')
  $element.attr('id', 'main')
  ```

  `attr`和`setAttribute`都是操作原有的html属性

- 原生js

  ```js
  elementDom.className = 'active'
  elementDom.setAttribute('id', 'main')
  ```

  记住，因为我们的js中`class`是保留字，所以js是很忌讳使用`class`来当作名字的。一般用来代替的是`className`(如getElementsByClassName)

2. 设置内容

- jQuery

  ```js
  $element.text('val')
  $element.html('val')
  $element.val('val')
  ```

- 原生js

  ```js
  elementDom.innerText = 'val'
  elementDom.innerHTML = 'val' // html全部大写!
  elementDom.value = 'val'
  ```

## 删除数组指定元素?

> 这个问题起因于蓝桥杯的web组的第8题，*有点小遗憾*

我们都知道添加数组元素很容易(`arr.push()`)，但是怎么删除一个元素呢？难道是`arr.pop()`?

`arr.pop()`是删除的最后一个数组元素，但是并不能删除指定的元素(*别指望括号里面可以传递索引*)

其实稍微想一下就知道：使用`arr.splice(index, num)`

<u>我们只需要传入index，然后将num置为一就可以了</u>
