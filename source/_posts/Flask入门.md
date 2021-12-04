---
title: Flask入门
date: 2021-08-14 12:19:10
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20210814101040.png
author: Rogers Kelamen
categories:
- 后端
tags:
- python
---


# Flask极速入门

> 首先咱们需要了解一下什么是Flask，它是一个轻量的Web框架。你可能还听说过Django，SpringBoot之类的Web框架，但是使用Flask的好处是简易并且好扩展(*你甚至可以使用任何的数据库，多种多样的模式*)。当然，作为刚开始接触后端接口的编写是极为容易上手的(*Perhaps*)

## 神说`Hello World`

```python
## helloworld.py
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello_world():
    return "hello world"


app.run(host="0.0.0.0")

```

然后你运行之后就可以在浏览器访问`127.0.0.1:5000`来查看你运行的web页面

### 代码解释

> 还是稍微解释一下吧

- 主要就是通过`Flask()`这个方法实例化了一个名叫`app`的flask实例对象
- 还有一点就是我们通过`@app.route()`这个装饰器(*其实是实例化对象的方法*)来处理路由，告诉flask我们的这个`/`路由上放的什么样的页面内容
- 最后就是`app.run()`来使这个实例跑起来吧，我个人估计里面应该还封装了各种监听方法，接受请求方法等，一个`run()`就能跑应该还是蛮方便的。在`run`里面的`host`参数是web实例跑在哪个ip地址，上面写`0.0.0.0`就是访问的ip(*默认是本地localhost*)

*你可以理解成之后每一个api接口都是一个方法*

## route详解

我们可以在`route`的参数中写一些特殊的路由，比如`/hello/<username>`，route会将这个尖括号中的字符串理解成任意的参数。也就是说我们可以获取这个路由中的`username`，然后当作实参使用

这个`route`甚至还提供类型定义:

```python
@app.route("/hello/<int: number>")
def blabla(number):
    ...
```

*我真的在写脚本语言吗XD*

### About Redirect

首先说一个官网上的flask默认项:

> 如果路由中尾部有一个斜杠(*Which looks like a directory*)，那么访问时没有加上斜杠(`/`)，Flask会自动进行重定向，帮你在尾部加一个(***Recommended***)

> 如果没有斜杠(`/`)但是访问时加了斜杠，就会出现404

接下来我们具体看看flask怎么简单实现重定向：

```python
from flask import redirect

@app.route("/baidu")
def baidu():
    return redirect("https://www.baidu.com")
```

*我们可以看到直接就是在方法中调用`redirect`方法就好了*

### About HTTP Method

直接上代码：

```python
## 代码仅示范用，不能运行
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()
```

*flask默认的method是`GET`*

### post接口实例

```python
@app.route("/test/my/first", methods=["POST"])
def first_post():
    my_json = request.get_json(True)
    user = my_json.get('username')
    print(user)
    return "success"
```

<font color=#34b>这里的`get_json`请求就是获取对post请求的数据，当然是json的，第一个参数是是否强行转换为json。不好使的时候可以设置为True</font>(*使用了`get_json之后你可以在python内部相当于转为了dict格式`*)

*有时候你需要解决跨域问题，直接安装一个`flask_cors`,然后*

```python
from flask_cors import CORS

CORS(app, resources=r'/*')
```

<font color=brown>pycharm开的虚拟环境是装不了`flask_cors`的</font>，你需要换解释器

#### 返回json数据

> 我们前端可以使用`JSON.stringify()`将对象转化为json格式，那么python中就是使用`jsonify`方法

```python
from flask import jsonify

@app.route("/test/my/first", methods=["POST"])
def first_post():
    my_json = request.get_json(True)
    user = my_json.get('username')
    user_age = my_json.get('age')
    user_age += 10

    return jsonify(name=user, age=user_age)
```

结果：

![我这里输入的年龄是12岁](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20210813211356.png)

![结果返回了22岁](https://gitee.com/rogerskelamen/mdpic/raw/master/img/20210813211529.png)

这个实例结合数据库的调用操作就可以实现一个真正意义上的简单的后台api接口了(*yeah!*)

#### 再完善一下

比如我们还可以对异常处理一下:

```python
@app.route("/test/my/first", methods=["POST"])
def first_post():
    try:
        my_json = request.get_json(True)
        user = my_json.get('username')
        user_age = my_json.get('age')
        if not all([user, user_age]):
            return jsonify(msg="缺少参数")
        user_age += 10
        return jsonify(name=user, age=user_age)
    except Exception as e:
        print(e)
        return jsonify(msg="出错了，请查看是否以正确格式访问")
```

## 真正的实例

```python
from flask import Flask, request, jsonify, session
from flask_cors import CORS

app = Flask(__name__)
CORS(app, resources=r'/*')
app.secret_key = b'sdfasdfasf'  # 设置app的密钥

# 登录
@app.route("/try/login", methods=["POST"])
def login():
    """
    帐号 username admin
    密码 password 123
    :return:
    """
    get_data = request.get_json(True)
    username = get_data.get("username")
    password = get_data.get("password")

    if not all([username, password]):
        return jsonify(msg="参数不完整")

    if username == "admin" and password == "123":
        # 如果验证通过 保存登录状态到session中
        session["username"] = username
        session.permanent = True

        return jsonify(msg="登录成功")
    else:
        return jsonify(msg="帐号或者密码错误")


# 检查登录状态
@app.route("/session", methods=["GET"])
def checkout():
    username = session.get("username")
    if username is not None:
        # 在这里写上数据库的查询等操作来验证是否存在这个用户
        return jsonify(username=username)
    else:
        return jsonify(msg="出错了，没有登录")  # 这里前端就可以配上路由跳转到登录


# 登出
@app.route("/try/logout", methods=["GET"])
def logout():
    session.clear()
    return jsonify(msg="成功登出")


app.run(host='127.0.0.2')
```

如果你想搞清楚cookie和session的区别以及flask是怎么使用session的，可以参考[这篇文章](https://juejin.cn/post/6982853755364605960)
