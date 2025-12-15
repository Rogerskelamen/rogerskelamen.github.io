---
title: Promise的诞生与本质
author: Rogers Kelamen
date: 2025-09-04 17:27:07
index_img: https://s2.loli.net/2025/09/04/LJ6QtSUdmp7KO4G.png
banner_img: https://s2.loli.net/2025/09/04/vxtha8UVGmYoRMg.png
tags:
- js
categories:
- 前端
---

# Promise的诞生与本质

大家都说`Promise`是用来解决回调地狱(`callback hell`)的，那回调地狱到底是什么？

```javascript
setTimeout(() => {
  console.log('first level');
  setTimeout(() => {
    console.log('second level');
    setTimeout(() => {
      console.log('third level');
    }, 1000);
  }, 1000);
}, 1000);
```

由于异步任务需要等待完成再进行处理（执行回调函数），那么如果另一个异步任务依赖于前一个异步任务，那么该异步任务就需要嵌套进回调函数中。多个依赖关系就会导致层层嵌套，最终形成一个长长的回调地狱（丢失可读性和可维护性）

`Promise`提供了一种机制从回调函数提取出关键数据，从而使用链式调用的形式在后续进行数据处理，避免直接在回调函数中处理。这样就从【嵌套】变成了【平铺】

```javascript
new Promise(resolve => {
  setTimeout(() => {
    resolve('first level');
  }, 1000);
})
  .then(result => {
    console.log(result);
    return new Promise(resolve => {
      setTimeout(() => {
        resolve('second level');
      }, 1000);
    });
  })
  .then(result => {
    console.log(result);
    return new Promise(resolve => {
      setTimeout(() => {
        resolve('third level');
      }, 1000);
    });
  })
  .then(result => {
    console.log(result);
  })
```

⚠️注意：**`Promise`只是提供了一种代码风格，他本身和异步没有任何关系！！！**

解释：因为现在很多异步任务为了避免回调地狱都封装了一层`Promise`，所以很多人将Promise和异步挂钩，实际上Promise只是做了代码风格调整。

## 题外内容——事件循环

在面试中，经常会将`Promise`内容和事件循环联系起来，考察代码执行的顺序。这里考察的点实际上就是`.then()`中的回调函数是微任务，需要放到微队列中，而且**微队列的优先级是最高的**

```javascript
setTimeout(() => {
  console.log('延时任务');
}, 0);
document.addEventListener('DOMContentLoaded', function() {
  console.log('交互任务');
})
Promise.resolve('微任务').then(result => console.log(result));
console.log('渲染主线程');
/*
顺序代表了每种任务的优先级，正确的打印顺序应该是(需开启live-server)：
渲染主线程
微任务
Live reload enabled.
交互任务
延时任务
*/
```

需要注意的是`Promise.resolve().then()`的方式和`new Promise(resolve => {}).then()`方式虽然都会产生微任务，但是`new Promise(resolve => {})`渲染主线程会直接执行，所以需要稍微注意一下：

```javascript
Promise.resolve('微任务1').then(result => console.log(result));
new Promise(resolve => {
  console.log('渲染主线程'); // 同步执行
  setTimeout(() => {
    resolve('微任务2');
  }, 0);
}).then(result => console.log(result));
/*
顺序是：
渲染主线程
微任务1
微任务2
*/
```

## 最终形态

随着`async/await`出现，现在异步任务的时候也不再使用`Promise`的链式了，而是回归到了以前同步代码的写法。

整个发展历程就是：【嵌套】 -> 【链式】 -> 【同步】

从处理方式通俗来说就是：<u>处理代码从写在回调函数中，变成写到链式的回调中，最后直接写到了下一行代码就行</u>

```javascript
const asyncTask = (msg) => {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve(msg);
    }, 1000);
  })
};

const res1 = await asyncTask('first level');
console.log(res1);
const res2 = await asyncTask('second level');
console.log(res2);
const res3 = await asyncTask('third level');
console.log(res3);
```
