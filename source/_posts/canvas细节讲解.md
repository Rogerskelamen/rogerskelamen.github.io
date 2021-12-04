---
title: canvas细节讲解
author: Rogers Kelamen
date: 2020-10-23 20:18:08
updated: 2020-10-27 21:42:00
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/1_sQuSbqYHW-38cJXo3RsvWA.png
banner_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/686d5abd64a01166aacba827.jpg
tags: 
- canvas
- js
categories: 
- 前端
---



# Canva的细节讲解（已断更）

## canva上下文

> 首先你得搞清楚CanvasRenderingContext2D（即上下文对象）和canvas的DOM对象是两个不一样的东西

**通过canvas的`getContext()`方法，我们可以获取一个canvas的上下文对象（<font color=#34b>注意，我没有说是一个上下文对象</font>）**

*而我们能不能再创建一个上下文对象呢？（在原有创建的基础上）*

warning：**坑来了，<font color=brown>每个`<canvas>`元素只有一个上下文对象</font>，我们每次调用`getContext()`方法都会返回相同的上下文对象**

> 我们再来说说第二个坑：
> canvasDOM元素和上下文对象傻傻分不清

warning：*我们每次都是用ctx（个人取名习惯，<u>下文皆用此名</u>）去绘制图形，而`canvas`只是跟其字面意思一样——“画布”罢了*

----

## 绘制图形三部曲（入门级）

1. `beginPath()`：新建路径
2. `moveTo()`：开始子路径
3. `lineTo()`：开始绘制

> 嗯嗯，别忘了他们都是ctx的方法。。。

---

## 当你想要闭合图形时~

一般来说，我们可以不用刻意的去用`lineTo()`来闭合图形，**因为当你使用`fill()`方法之后，图形的看上去就已经闭合上了**（*然而轮廓没有闭合）*

但事实上，不管你用`lineTo()`还是`fill()`都是没有真正意义上闭合上的**（`lineTo()`只是表现出几条线段共用了一个起点和一个终点）**

所以：**<font color=#34b>当你想闭合图形时，用`closePath()`方法吧</font>**

----

## 图形属性

> 个人认为这个部分比较重要，就列出了以下的表格
> （都是一些常用的）

| 属性名       | 含义                     |
| ------------ | ------------------------ |
| fillStyle    | 设置填充颜色和渐变，图案 |
| font         | 设置文本时的CSS字体      |
| globalAlpha  | 设置全局透明度           |
| lineWidth    | 外框线的宽度             |
| textAlign    | 文本水平对齐方式         |
| textBaseLine | 文本垂直对齐方式         |
| shadwoColor  | 下拉阴影的颜色           |
| strokeStyle  | 勾勒线的颜色，渐变，图案 |

**<font color=brown>看清楚，这些个都是属性，而不是ctx的方法！</font>**

eg:

```js
ctx.fillStyle = 'red';
// 而不是
ctx.fillStyle('red');	// 这锅是错滴
```

---

## 圆弧的部分细节

### 关于`arc()`

<u>六个参数：圆心x，圆心y，radius，起始弧度，结束弧度，是否顺时针（false/true）</u>

*除了参数难记，很多情况下会忘记以下这点：*

**<font color=brown>它会首先将当前点和弧形的起点用一条直线连接</font>**，要是不注意这点，可能会变成这样：

![](https://gitee.com/rogerskelamen/mdpic/raw/master/img/arc.png)

所以可以直接用

- `moveTo`移到起始点，或者
- 干脆`beginPath`重开一局

### 弧度制？角度制？

> 好吧，你稍微看一下文档就知道`arc()`的第四五个参数是弧度制
> 但是我们总是钟爱角度制一样想都不想就用角度制😂

所以，我们有必要整一个工具函数（<font color=#34b>极度推荐在用canvas画图时提前把这个函数写上去</font>）

```js
function rads(x){
    return Math.PI * x / 180;
}
```

*之后就可以直接调用`rads()`。*

### `rads(0)`究竟在哪？

**A：<u>位于圆心x轴正方向与圆弧的交点</u>**

---

## 矩形部分细节

> 矩形四大方法：`fillRect()`、`strokeRect()`、`clearRect()`、`rect()`(这个用到的极少)

- `fillRect()`和`strokeRect()`

  两者很相似，参数都是：前两个指定矩形的左顶点，后两个指定矩形的宽和高。

  **前者使用<u>当前的`fillStyle`属性来填充矩形</u>，而后者使用<u>当前的`strokeStyle`属性来勾勒外边框</u>**

- `clearRect()`

  参数也是一样的，但是使用<u>透明的空画布</u>来填充矩形。

- `rect()`

  warning；*前几个方法都不会修改当前的路径，但是这个会改变*

  *而且它就是只有改变路径这一个功能了（你无法直观的从canvas上看出效果），着实鸡肋。*

---

## 关于背景图片

> 要使用背景图片的图案而不是颜色来填充或勾勒（有勾勒这种操作么），可以将`fillStyle`或者`strokeStyle`属性设置成`CanvasPattern`对象，该对象可以通过调用ctx的`createPattern()`方法返回相应的值

eg：

```js
var image = document.getElementById('myimage');
ctx.fillStyle = ctx.createPattern(image, 'repeat');
```

注意：**`createPattern()`的第一个参数必须是一个`<img>`元素，`<canvas>`元素或`<video>`元素（<font color=#34b>或者是通过`Image()`构造出的图片元素</font>）**







---

## 杂谈

> 如果你用的是vscode编辑器来编写的这些和canvas有关的代码，你会发现vscode默认不会提供代码补全，这里讲一个方法：

**在获取canvas元素的前一行写上如下注释：**

```js
/** @type {HTMLCanvasElement} */
```

*则在之后vscode就能识别出是canvas元素并给出代码补全了*

---

