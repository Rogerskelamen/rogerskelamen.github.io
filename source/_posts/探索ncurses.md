---
title: 从termcap到pager：探索ncurses
date: 2023-05-23 23:18:26
index_img: https://s2.loli.net/2023/05/24/WhjiFAYwXJT7IK9.png
banner_img: https://s2.loli.net/2023/05/24/gvtzEriTJFuy4nm.png
tags:
- linux
- miscellaneous
categories:
- 工具
---

# 从termcap到pager：探索ncurses

## Intro

本篇文章的起因是我想要模仿GNU/Linux的more或者less这样的pager写出一个代码阅读工具。本来自己尝试使用标准库纯粹从零开始写，但经过探索过后发现还是调用ncurses库更为科学合理一些(*不然就要独自和终端控制字符死磕到底了*)。由于本人在实现过程中经历了这样复杂的心路历程，所以文章结构不会是简单介绍ncurses的功能和使用，而是从ncurses的起源，也就是ANSI转义字符开始，逐渐道出ncurses的由来，并最终使用ncurses实现一个简单的pager。

## ANSI escape codes

可能你对于[ANSI转义字符](https://zh.wikipedia.org/zh-cn/ANSI%E8%BD%AC%E4%B9%89%E5%BA%8F%E5%88%97)(ANSI escape codes)不太熟悉，但是这确实就是我们故事的开始。

想想看，我们想要实现一个pager，那么最基础的几个功能得有吧：

- 光标的移动

- 屏幕内容的更新(*或者简单来说就是翻页*)

而神奇的ANSI转义字符就可以帮助我们实现这些，e.g.

```txt
\033[1C
```

就是一段ANSI转义字符，而它的作用就是让光标向右移动一个字符

这种特殊的字符格式一般是escape字符的转义(*oct:\033;hex:\u001b*)作为开头，然后是一个`[`，接着就是有各自功能的自定义字符。比如下面几种也是ANSI escape codes:

```txt
# 使字符变成红色
\033[31m
# 使字符背景变成蓝色
\033[44m
# 重置颜色
\033[0m
```

这样一来，我们就知道实现pager肯定是需要这些特殊字符的。<u>关于ANSI escape codes的更多具体介绍和运用可以参考[我的另一篇文章](https://www.cnblogs.com/rokelamen/p/16748739.html)</u>

## 从头实现的困难

我们对于如何实现pager的基本操作已经有了思路，但是我们还需要解决一个问题。

试想我们的pager程序，应该是用户输入一个字符(*屏幕中不显示*)然后程序立即调用相应的ANSI转义字符。但是现实情况是，我们的终端默认处于一种叫做canonical mode的模式中，在此模式下，用户往stdin中输入字符是自动显示到终端上，并且只有当我们按<kbd>Enter</kbd>之后，字符内容才会送到程序中去。这种模式的好处是如果用户输入错误可以使用<kbd>Backspace</kbd>来撤销已输入的字符，但显然这不是我们的pager程序想要的。

于是我们得使用另一种叫做raw mode的模式，这种模式就是用户在终端输入字符后立即送往当前程序。不过说来容易做来难，我们没有特别的捷径来让终端转换到raw mode中，所以我们得手动操作。编写以下函数来手动转换:

```c
static void
enraw()
{
  tcgetattr(STDIN_FILENO, &orig_termios);

  struct termios raw = orig_termios;
  raw.c_iflag &= ~(ICRNL | IXON);
  raw.c_lflag &= ~(ECHO | ICANON | IEXTEN | ISIG);
  tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw);
}
```

有了raw mode之后，我们可以简单实现一个Vim-like的文本输出程序(*输出对象是标准输出*):

```c
/* inspired by https://viewsourcecode.org/snaptoken/kilo/02.enteringRawMode.html
 * keypress explanation:
 * C-k/j/h/l: up/down/left/right
 * C-q: quit the program
 */
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>
#include <unistd.h>
#include <termios.h>

#define CTRL_KEY(k) ((k) & 0x1f)

static struct termios orig_termios;

static void enraw();
static void disraw();
static void die(const char *s);

static void
enraw()
{
  if (tcgetattr(STDIN_FILENO, &orig_termios) == -1)
    die("tcgetattr");
  atexit(disraw); // switch back to default canonical mode when program exits

  struct termios raw = orig_termios;
  raw.c_iflag &= ~(ICRNL | IXON);
  raw.c_lflag &= ~(ECHO | ICANON | IEXTEN | ISIG);
  if (tcsetattr(STDIN_FILENO, TCSAFLUSH, &raw) == -1)
    die("tcsetattr");
}

static void
disraw()
{
  if (tcsetattr(STDIN_FILENO, TCSAFLUSH, &orig_termios) == -1)
    die("tcsetattr");
}

// error handler
static void
die(const char *s)
{
  perror(s);
  exit(1);
}

int main()
{
  write(STDIN_FILENO, "\033[2J", 4); // clear the screen
  enraw(); // switch terminal to raw mode

  char c;
  while (read(STDIN_FILENO, &c, 1) == 1 && c != CTRL_KEY('q')) {
    switch (c) {
      case CTRL_KEY('h'):
        printf("\033[1D");
        break;
      case CTRL_KEY('j'):
        printf("\033[1B");
        break;
      case CTRL_KEY('k'):
        printf("\033[1A");
        break;
      case CTRL_KEY('l'):
        printf("\033[1C");
        break;
      case '\r': // key enter is '\r', so we must translate it to '\n'
        printf("\n");
        break;
      default:
        printf("%c", c);
    }
    fflush(stdout);
  }

  return 0;
}
```

尽管我们确实可以就这么一直使用ANSI转义字符来完成各种终端控制指令，最终实现我们想要的程序，但是很明显其过程中会有海量细节等着我们，我们很难也不应该去应付这些琐碎的细节。并且另一个坏消息是，不同类型的终端有着不同的控制数据库(*早期由termcap管理*)，这意味着它们的ANSI转义字符的标准不一定是相同的，我们可能将面临编写多份代码来应对不同终端的兼容等问题。于是我们急需一个工具来拯救我们的头发，那就是ncurses。

## NCURSES

[ncurses](https://zh.wikipedia.org/zh-cn/Ncurses)最早起源于curses工具(*cursor optimization*)，在unix早期使用curses就已经能解决前一节中的部分问题。后来System V Release 4.0(SVr4)发布后，ncurses作为curses的克隆库被开发出来，并加入了一些新的扩展特性。其中，ncurses的'n'就是'new'的意思。

你可能在之前已经接触到了由ncurses编写的图形界面程序，比如当你想要看视频而为Linux安装视频解码器的时候，或者是当你写了一个新module想将其安装到Linux上去的时候，你都会看到一个类似下图的GUI：

![menuconfig](https://s2.loli.net/2023/05/24/bod6nDlSpCqrPHY.png)

想要自行学习或了解ncurses的话，可以去阅读[这篇HOWTO文章](https://tldp.org/HOWTO/NCURSES-Programming-HOWTO/index.html)，当然，本文接下来的ncurses介绍部分也是基于它的。

那么我们可以试试运用一下ncurses，**写一个Hello World**，对ncurses有一个感性认识:

```c
#include <curses.h>

int main()
{
  initscr();              // initial curses mode
  printw("hello, world"); // print hello world
  refresh();              // print it one the real screen
  getch();                // wait for user input
  endwin();               // end curses mode

  return 0;
}
```

*如果想要编译的话还要加上ncurses的链接库:*

```bash
gcc hello.c -o hello -lncurses
```

这个`hello`程序会默认清空屏幕并且打印字符串`hello, world`，当你按下任意按键的时候程序退出。

如果想要开启raw mode，只需要调用`raw()`就可以了，同时使用`noecho()`来不显示用户的输入。

当我们想要移动光标时，就可以调用`move(row, col)`，它会将光标移动到第row行，第col列。所以我们可以仿照前一节的程序写一个功能一样的输出器:

```c
#include <curses.h>

#define KEY_CTRL(k) ((k) & 0x1f)

static int x = 0;
static int y = 0;

static void
init()
{
  initscr();
  raw();    // raw mode
  noecho(); // switch to non-echo
  keypad(stdscr, TRUE); // enable function key(F1,Up/Down...)
}

int main()
{
  init();
  int c;
  int row, col;
  getmaxyx(stdscr, row, col); // get size of screen

  while ((c = getch()) != KEY_CTRL('q')) {
    if (c == KEY_CTRL('h') || c == KEY_LEFT) {
      x = x - 1 < 0 ? 0 : x - 1;
      move(y, x);
    } else if (c == KEY_CTRL('j') || c == KEY_DOWN) {
      y = y + 1 > col ? col : y + 1;
      move(y , x);
    } else if (c == KEY_CTRL('k') || c == KEY_UP) {
      y = y - 1 < 0 ? 0 : y - 1;
      move(y, x);
    } else if (c == KEY_CTRL('l') || c == KEY_RIGHT) {
      x = x + 1 > row ? row : x + 1;
      move(y, x);
    } else {
      printw("%c", c);
    }
    refresh();
  }

  endwin();

  return 0;
}
```

整体看起来简单了一些(*逻辑也清晰了一些*)，至少我们不必再去烦心于ANSI转义字符和终端模式的转换了。

## 实现一个简单的pager

想要实现一个pager，我们首先明确几个实现目标:

1. 程序需接受命令行中的第二个参数作为文件名，并给出相关的文件处理

2. 程序需将文件内容存入某个ncurses缓存区中，然后根据需要展示部分的区域

3. 程序需能够接受用户的动态输入，以便让用户控制程序行为

其中为了实现第二个目标，这里需引入一个新的ncurses概念pad(*curs_pad(3)*)。我们可以将文件的内容全部存储于一个pad中，然后通过`prefresh()`函数(*这里类似于`refresh`*)将pad中的部分内容展示给stdscr。

`prefresh()`接受参数为：目标pad，需要pad的哪部份区域，展示在stdscr中的哪里

比如这里有一个10x10的pad展示在stdscr特定区域的例子：

```c
#include <curses.h>

int main()
{
  int i;
  WINDOW *pad;

  initscr();

  pad = newpad(10, 10);
  mvaddstr(30, 0, "prefresh(pad, 0, 0, 0, 3, 9, 12);");
  refresh();

  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");
  waddstr(pad, "ABCDEFGHIJ");

  prefresh(pad, 0, 0, 0, 3, 9, 12);

  wgetch(pad);

  delwin(pad);
  endwin();
  return 0;
}
```

在此我需要解释一下为什么我们必须要使用pad这种策略，毕竟我们可以直接使用stdscr然后调用`scrl()`函数(*curs_scroll(3)*)简单地完成翻页。诚然，我们的主要目标就是滚动翻页，看似我在此复杂化了问题，但是真实情况是：如果使用`scrl()`函数，超出屏幕上下限的内容会因为滚动而丢失。**注意，是永远丢失!**实际上stdscr是不会自行保存所有打印内容的，所以我们必须找到一种绝对可以保证文本内容不会丢失的安全数据结构，于是便找到了pad。

由此我们具备了所有编写pager的前置知识，下面就是愉快的编码了！

```c
/* a simple pager program with vim-like keymap
 * help manual:
 * q: quit program
 * j: scroll one line down
 * k: scroll one line up
 * d: scroll half screen down
 * u: scroll half screen up
 * g: goto first line
 * G: goto last line
 */
#include <curses.h>
#include <stdlib.h>

static FILE *fp;
static int mrow, mcol; // size of standard screen

static void
usage(char *cmd)
{
  printf("usage: %s filename\n", cmd);
  exit(1);
}

static void
init()
{
  initscr();
  raw();    // raw mode
  noecho(); // switch to non-echo
  keypad(stdscr, TRUE); // enable function key(F1,Up/Down...)
  getmaxyx(stdscr, mrow, mcol); // get size of standard screen
  curs_set(0); // turn cursor off
}

int main(int argc, char *argv[])
{
  if (argc != 2) {
    usage(argv[0]);
  }
  if ((fp = fopen(argv[1], "r")) == NULL) {
    fprintf(stderr, "%s: can't open file %s\n", argv[0], argv[1]);
    exit(2);
  }
  int c;
  int lcnt = 0;
  while ((c = fgetc(fp)) != EOF) {
    if (c == '\n') { // it only fit to unix format text file(not DOS)
      lcnt++;
    }
  }

  // initialization
  init();
  WINDOW *pad = newpad(lcnt, mcol);

  rewind(fp);
  while ((c = fgetc(fp)) != EOF) {
    pechochar(pad, c);
  }
  fclose(fp);

  int padrow = 0;
  prefresh(pad, padrow, 0, 0, 0, mrow - 1, mcol - 1);

  while ((c = wgetch(pad)) != 'q') {
    switch (c) {
      case 'j':
        if (padrow < lcnt - mrow) {
          padrow++;
        }
        break;
      case 'k':
        if (padrow > 0) {
          padrow--;
        }
        break;
      case 'd':
        if (padrow + mrow/2 <= lcnt - mrow) {
          padrow += mrow/2;
        }else {
          padrow = lcnt - mrow;
        }
        break;
      case 'u':
        if (padrow >= mrow/2) {
          padrow -= mrow/2;
        }else {
          padrow = 0;
        }
        break;
      case 'g':
        padrow = 0;
        break;
      case 'G':
        padrow = lcnt - mrow;
        break;
      default:
        break;
    }
    prefresh(pad, padrow, 0, 0, 0, mrow - 1, mcol - 1);
  }

  // clean up
  delwin(pad);
  endwin();
  exit(0);
}
```

尽管这个pager看起来不错，但这个程序还存在一个缺陷，那就是当文件中一行的内容超过屏幕列数的话，超出的部分就无法显示了。简单来说就是没有wrap的功能。

当然我不打算进一步改进了(*我对它还比较满意*)，你可以将此作为一个小练习以验证自己所学(*这并不困难！*)

