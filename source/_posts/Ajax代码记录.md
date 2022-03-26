---
title: Ajax代码记录
date: 2021-08-16 17:35:54
index_img: https://s2.loli.net/2022/03/26/KCIHXRQpdwLgeNS.png
author: Rogers Kelamen
tags:
- js
categories:
- 前端
---

# Ajax请求

> 因为本人老是忘记怎么使用Ajax，所以还是在这里记一份

## JQuery Realization

```js
$.ajax({
    url: 'https://test:5000',
    type: 'post',
    dataType: 'json',
    data: JSON.stringify(),
    withCredentials: true,
    success: function(res){
        console.log(res);
    },
    error: function(res){
        console.log(res.msg);
    }
})
```

## JS XHR Realization

> 虽然原生JS不太方便，但是我们还是要了解一下底层的东西对吧XD

- 仅有get的(*便于理解*)

```js
function originAjax() {
    var xhr = new XMLHttpRequest();
    xhr.onreadystatechange = function () {
        if (xhr.readyState == 4) {
            var data = xhr.responseText;
            console.log(data);
        }
    };
    xhr.open("get", "http://mocktest");
    xhr.send();
}
```

- 完整版(*摘自[知乎](https://zhuanlan.zhihu.com/p/64167474)*)

```js
var Ajax={
    get: function(url, fn) {
      // XMLHttpRequest对象用于在后台与服务器交换数据   
       var xhr = new XMLHttpRequest();            
       xhr.open('GET', url, true);
       xhr.onreadystatechange = function() {
           // readyState == 4说明请求已完成
           if (xhr.readyState == 4 && xhr.status == 200 || xhr.status == 304) { 
               // 从服务器获得数据 
               fn.call(this, xhr.responseText);  
           }
       };
       xhr.send();
   },
   // datat应为'a=a1&b=b1'这种字符串格式，在jq里如果data为对象会自动将对象转成这种字符串格式
    post: function (url, data, fn) {
      var xhr = new XMLHttpRequest();
      xhr.open("POST", url, true);
      // 添加http头，发送信息至服务器时内容编码类型
      xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");  
      xhr.onreadystatechange = function() {
          if (xhr.readyState == 4 && (xhr.status == 200 || xhr.status == 304)) {
              fn.call(this, xhr.responseText);
          }
      };
      xhr.send(data);
  }
}
```

1. open(method, url, async) 方法需要三个参数:

    method：发送请求所使用的方法（GET或POST）；与POST相比，GET更简单也更快，并且在大部分情况下都能用；然而，在以下情况中，请使用POST请求：

    - 无法使用缓存文件（更新服务器上的文件或数据库）
    - 向服务器发送大量数据（POST 没有数据量限制）
    - 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

    url：规定服务器端脚本的 URL(该文件可以是任何类型的文件，比如 .txt 和 .xml，或者服务器脚本文件，比如 .asp 和 .php （在传回响应之前，能够在服务器上执行任务）)；

    async：规定应当对请求进行异步（true）或同步（false）处理；true是在等待服务器响应时执行其他脚本，当响应就绪后对响应进行处理；false是等待服务器响应再执行。

2. send() 方法可将请求送往服务器。

3. onreadystatechange：存有处理服务器响应的函数，每当 readyState 改变时，onreadystatechange 函数就会被执行。

4. readyState：存有服务器响应的状态信息。（更加具体的状态码信息可自行百度）

- 0: 请求未初始化（代理被创建，但尚未调用 open() 方法）
- 1: 服务器连接已建立（open方法已经被调用）
- 2: 请求已接收（send方法已经被调用，并且头部和状态已经可获得）
- 3: 请求处理中（下载中，responseText 属性已经包含部分数据）
- 4: 请求已完成，且响应已就绪（下载操作已完成）

5. responseText：获得字符串形式的响应数据。

6. setRequestHeader()：POST传数据时，用来添加 HTTP 头，然后send(data)，注意data格式；GET发送信息时直接加参数到url上就可以，比如url?a=a1&b=b1。
