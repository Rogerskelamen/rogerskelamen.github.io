---
title: X-macro
date: 2022-09-29 14:55:13
banner_img: https://s2.loli.net/2022/09/29/VdahBKX4FZPMRQ8.jpg
index_img: https://s2.loli.net/2022/09/29/y9jR1TmdrGcsb7h.png
tags:
- c
categories:
- 编程语言
---

# X-macro

> 首先需要明确的是，X-macro是一种可以让我们的C语言编写更加便捷高效的编程技巧，它由宏的语法演化出来。<u>而并不是一种编程语法</u>

## 1. 使用宏的函数

在我们学习C语言语法时，我们接触到宏无非就是进行变量定义，如：

```c
#define macro value
```

除此之外宏还可以定义简单的函数：

```c
#define macro(X) X_process
```

## 2. 使用换行

我们知道，宏本质来说就是简单的字符串替换，我们的宏定义是一行行的，如果我们想要换行，接着上次的定义写，就需要这样：

```c
#define macro process1 \
              process2
```

<u>定义行尾加上`\`即可</u>

## 3. 使用原字符和字符拼接

> 在函数形式的宏定义中，我们的参数在运行中被转化成了实参，不再是简单的字符串替换了。如果我们需要的就是这个字符而不是参数怎么办呢？

1. 使用`#`代表原字符

   ```c
   #define macro(X) printf(#X " = %d ", X);
   ```

   这个函数将运行`printf("X = %d ", X);`

2. 使用`##`进行字符拼接

   ```c
   #define macro(X) int X, X##1;
   ```

   这里就是声明定义了`X`和`X1`

