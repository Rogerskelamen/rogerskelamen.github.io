---
title: cmus使用
date: 2021-07-25 09:56:04
index_img: https://gitee.com/rogerskelamen/mdpic/raw/master/img/20210725100040.png
tags:
- geek
---

# the Usage of Cmus

| key            | function           |
|----------------|--------------------|
| <kbd>c</kbd>   | pause/unpause      |
| <kbd>h/l</kbd> | back/go 10 seconds |
| <kbd>z/b</kbd> | previous/next song |

## Mode

| mode         | key          |
|--------------|--------------|
| 单曲循环     | <kbd>r</kbd> |
| 列表播放     | <kbd>C</kbd> |
| 随机播放     | <kbd>s</kbd> |
| 添加到下一曲 | <kbd>e</kbd> |

## the Playlists(view 3)

这个功能类似于自己开创的歌单，可以通过在选中的歌曲时按<kbd>y</kbd>将此歌曲添加到playlists中，初始时会有默认的歌单`default`

新建歌单 :pl-create

导出歌单 :pl-export

导入歌单 :pl-import [filename]

创建或切换到目标歌单：

按 3 进入playlist模式, `:pl-create`, 在这个界面中，执行上下键选择目标歌单,按 空格 将其定义为当前操作对象, 按 5 进入文件浏览模式, 选择目标音频文件或整个文件夹，按 y 将其添加到目标歌单

## Copying Tracks Between Views

You can copy marked or selected tracks from views 1-5.

| key | func                                   |
|-----|----------------------------------------|
| a   | copy tracks to the library (1-2)       |
| y   | copy tracks to the marked playlist (3) |
| e   | append tracks to the play queue (4)    |
| E   | prepend tracks to the play queue (4)   |


## 按键详解

| key                                                                           | mean                       | 中文解释                                                                         |
| :-----------------------------------------------------------                  | -------------------------- | ------------------------------------------------------------                     |
| [q]                                                                           | quit -i                    | 退出                                                                             |
| [^C]                                                                          | :quit<enter> to exit cmus. | 退出（^就是ctrl的意思）                                                          |
| [b]                                                                           | player-next                | 下一首                                                                           |
| [c]                                                                           | player-pause               | 暂停                                                                             |
| [x]                                                                           | player-play                | 播放                                                                             |
| [z]                                                                           | player-prev                | 前一首                                                                           |
| [v]                                                                           | player-stop                | 停止                                                                             |
| [^L]                                                                          | refresh                    | 刷新                                                                             |
| [n]                                                                           | search-next                | 查找下一个                                                                       |
| [N]                                                                           | search-prev                | 查找上一个                                                                       |
| [.]                                                                           | seek +1m                   | 快进                                                                             |
| [l], [right]                                                                  | seek +5                    | 快快进                                                                           |
| [,]                                                                           | seek -1m                   | 快退                                                                             |
| [h], [left]                                                                   | seek -5                    | 快快退                                                                           |
| [m]                                                                           | toggle aaa_mode            | 修改aaa_mode模式 （all from library / artist from library / album from library） |
| [C] (Continue)                                                                | toggle continue            | 持续播放，也就是说这首播放完了继续播放下一首                                     |
| [M]                                                                           | toggle play_library        | 切换到playlist                                                                   |
| [o]                                                                           | toggle play_sorted         | 切换成all from sorted library                                                    |
| [r] (Repeat)                                                                  | toggle repeat              | 循环                                                                             |
| [^R]                                                                          | toggle repeat_current      | 对当前循环                                                                       |
| [t]                                                                           | toggle show_remaining_time | 显示剩余时间（默认是显示播放时间）                                               |
| [s]                                                                           | toggle shuffle             | 乱序播放                                                                         |
| [F]                                                                           | push filter<space>         | 不清楚，只看到命令提示行显示了:fliter ，估计是提示筛选神马的                     |
| [L]                                                                           | push live-filter<space>    | 我也不清楚，命令显示为：:live-filter， 看着想即时筛选？                          |
| [i]                                                                           | toggle show-hidden         | 显示/取消显示隐藏文件                                                            |
| [1]                                                                           | view tree                  | 主界面                                                                           |
|                                                                               | [2]                        | view sorted                                                                      |
| [3]                                                                           | view playlist              | 显示播放列表                                                                     |
| [4]                                                                           | view queue                 | 显示Queue （我也不知道是啥东西）                                                 |
| [5]                                                                           | view browser               | 显示文件目录                                                                     |
| [6]                                                                           | view filters               | 显示筛选结果（？）                                                               |
| [7]                                                                           | view settings              | 显示快捷键                                                                       |
|                                                                               | [!]                        | push shell<space>                                                                |
| []]                                                                           | vol +0 +1                  | 音量控制，左声道不变，右声道+1                                                   |
| [[]                                                                           | vol +1 +0                  | 音量控制，左声道+1，右声道+0                                                     |
| [+], [=]                                                                      | vol +10%                   | 声音变大10%                                                                      |
| [-]                                                                           | vol -10%                   | 声音减小10%                                                                      |
| [}]                                                                           | vol -0 -1                  | 音量控制，左声道不变，右声道-1                                                   |
| [{]                                                                           | vol -1 -0                  | 音量控制，左声道+0，右声道+1                                                     |
| [enter]                                                                       | win-activate               | 激活                                                                             |
| [E]                                                                           | win-add-Q(uery)            | 将track拷贝至列表                                                                |
| [a]                                                                           | win-add-l(ibrary)          | 将track拷贝至库                                                                  |
| [y]                                                                           | win-add-p(lalist)          | 将track拷贝至播放列表                                                            |
| [G], [End]                                                                    | win-bottom                 | 跳至列表底                                                                       |
| [g], [Home]                                                                   | win-top                    | 跳至列表头                                                                       |
| [u]                                                                           | win-update                 | 更新                                                                             |
| [k], [up]                                                                     | win-up                     | 光标向下移动一行                                                                 |
| [j], [down]                                                                   | win-down                   | 光标向上移动一行                                                                 |
| [backspace]                                                                   | browser-back               | 返回上一级目录                                                                   |
| [:clear -l]                                                                   |                            | 清空Library                                                                      |
| [:clear -p]                                                                   |                            | 清空Playlist（播放列表）                                                         |
| [:clear -q]                                                                   |                            | 清空query（队列）                                                                |
| [:add -l]                                                                     |                            | 添加至Library（库)                                                               |
| [:add -p]                                                                     |                            | 添加至Playlist（播放列表）                                                       |
| [:add -q]                                                                     |                            | 添加至query（队列)                                                               |
| [:colorscheme green / cyan / green-mono-88 / gray-88 / xterm-white / zenburn] |                            | 切换主题                                                                         |