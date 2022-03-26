---
title: Pygame使用心得
date: 2021-09-21 16:00:44
author: Rogers Kelamen
index_img: https://s2.loli.net/2022/03/26/4rR6AZIwx3Qtn7L.png
banner_img: https://s2.loli.net/2022/03/26/2y3bR9daDHQrp4O.png
tags:
- game
categories:
- 后端
---

# pygame使用心得

> 想要使用简单的代码来编写游戏选择pygame就对了 XD

> 同时，pygame也是最好的python图形化库(*它同时支持方便的图片，音视频的嵌入*)，你可以通过`pygame`应用简单地实现很多图形化需求的程序，比如`Bad Apple`😝

*此markdown文档用来记录本人学习pygame中的各种经历*

## Basic Framework

```python
import pygame
pygame.init()

win = pygame.display.set_mode((500, 500))
pygame.display.set_caption("First Game")

run = True
while run:
    pygame.time.delay(40)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            run = False

pygame.quit()
```

**主要在`while`循环中写主程序的代码**

解释一下：首先的`import-init-quit`结构是必不可少的，`win`是这个游戏的窗口对象，`time.delay`决定了游戏的速度；`for event in pygame.event.get()`得到了所有的发生事件

## 事件记录

你可以写一个事件脚本来实验一下pygame会触发多少个事件：

```python
#!/usr/bin/env python3

import pygame
from pygame.locals import *
from sys import exit

pygame.init()

SCREEN_SIZE = (640, 480)
screen = pygame.display.set_mode(SCREEN_SIZE, 0, 32)

font = pygame.font.SysFont("arial", 16)
font_height = font.get_linesize()

event_text = []

while True:
    event = pygame.event.wait()
    # 获得时间名称
    event_text.append(str(event))

    # 这个切片操作保证了event_text里面只保留一个屏幕的文字
    event_text = event_text[-SCREEN_SIZE[1] // font_height:]

    if event.type == QUIT:
        exit()

    screen.fill((0, 0, 0))

    y = SCREEN_SIZE[1] - font_height

    for text in reversed(event_text):
        screen.blit(font.render(text, True, (0, 255, 0)), (0, y))

        # 把笔提一行
        y -= font_height

    pygame.display.update()
```

- ### MOUSEMOTION事件

这个事件有三个参数：

1. `buttons`: 一个含有三个数字的元组，三个值分别代表左键、中键和右键，1就是按下了。

2. `pos`: 就是位置呗。。(position)

3. `rel`: 代表了现在距离上次产生鼠标事件时的距离(relative)

当然和MOUSEMOTION类似，我们还有MOUSEBUTTONDOWN和MOUSEBUTTONUP两个事件，看名字就明白是什么意思了。很多时候，你只需要知道鼠标点下就可以了，那就可以不用上面那个比较强大（也比较复杂）的事件了。它们的参数为：

1. `button`: 看清楚少了个s，这个值代表了哪个按键被操作

2. `pos`: 和上面的一样

- ### 处理键盘事件

其实当我们按下并放开了键盘中的一个按键时，会触发三个事件：KeyDown, TextInput, KeyUp (*除了一些无法输入到文本中的按键以外，如<kbd>Alt</kbd>就不会有TextInput事件*)

#### KEYDOWN和KEYUP

- key – 按下或者放开的键值，是一个数字，估计地球上很少有人可以记住，所以Pygame中你可以使用`K_xxx`来表示，比如字母a就是`K_a`，还有`K_SPACE`和`K_RETURN`等。

- mod – 包含了组合键信息，如果mod & `KMOD_CTRL`是真的话，表示用户同时按下了Ctrl键。类似的还有`KMOD_SHIFT`，`KMOD_ALT`。

- unicode – 代表了按下键的Unicode值，这个有点不好理解，真正说清楚又太麻烦，游戏中也不太常用，说明暂时省略，什么时候需要再讲吧。

### 事件过滤

并不是所有的事件都需要处理的，比如，俄罗斯方块就无视你的鼠标，而在游戏场景切换的时候，你按什么都是徒劳的。我们应该有一个方法来过滤掉一些我们不感兴趣的事件（当然我们可以不处理这些没兴趣的事件，但最好的方法还是让它们根本不进入我们的事件队列，我们使用`pygame.event.set_blocked(事件名)`来完成。如果有好多事件需要过滤，可以传递一个列表，比如`pygame.event.set_blocked([KEYDOWN, KEYUP])`，如果你设置参数None，那么所有的事件有被打开了。与之相对的，我们使用`pygame.event.set_allowed()`来设定允许的事件。

### 产生事件

通常玩家做什么，Pygame就产生对应的事件就可以了，不过有的时候我们需要模拟出一些事件来，比如录像回放的时候，我们就要把用户的操作再现一遍。

为了产生事件，必须先造一个出来，然后再传递它：

```python
my_event = pygame.event.Event(KEYDOWN, key=K_SPACE, mod=0, unicode=u' ')
# 你也可以像下面这样写，看起来比较清晰（但字变多了……）
my_event = pygame.event.Event(KEYDOWN, {"key":K_SPACE, "mod":0, "unicode":u' '})

pygame.event.post(my_event)
```


你甚至可以产生一个完全自定义的全新事件，有些高级的话题，暂时不详细说，仅用代码演示一下：


```python
CATONKEYBOARD = USEREVENT+1
my_event = pygame.event.Event(CATONKEYBOARD, message="Bad cat!")
pygame.event.post(my_event)

#然后获得它
for event in pygame.event.get():
    if event.type == CATONKEYBOARD:
        print event.message
```


## 显示相关

使用`pygame.display.set_mode()`函数可以创建一个标准的pygame游戏窗口

### 全屏显示

利用第二个参数：

```python
screen = pygame.display.set_mode((640, 480), FULLSCREEN, 32)
```

*warning：如果你的程序有什么问题，很可能进入了全屏模式就不太容易退出来了，所以最好先用窗口模式调试好，再改为全屏模式。*

在全屏模式下，显卡可能就切换了一种模式，你可以用如下代码获得您的机器支持的显示模式：

```python
import pygame
pygame.init()
pygame.display.list_modes()
```

## pygame for Mac m1

就算你在搭载了m1芯片的MacBook上使用`homebrew`安装了所有的依赖，但是，你使用`pip3`默认安装了`pygame`，于是你发现很多的模块都用不了。

*你明明安装了`sdl-mixer`，但是就是载入不了音频；你明明安装了`sdl-image`，但是`pygame`还是提示你需要载入`.bmp`格式的图片(此时你是否有很多的问号？😖)*

不要再用`pip3`默认安装的`pygame`了，请看[这篇文章](https://zhuanlan.zhihu.com/p/400203758)

## Problem

当你使用`pygame.mixer.music.load()`时，你发现报错了：格式不对🤔。这是因为你使用了`.mp3`格式，但是游戏程序一般只支持`.ogg`或者`.wav`啥的，所以你需要转格式(*不久的将来可能会支持mp3?*)

