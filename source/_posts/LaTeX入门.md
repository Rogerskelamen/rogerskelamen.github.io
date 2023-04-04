---
title: LaTeX入门
math: true
index_img: https://s2.loli.net/2022/03/26/aKEw7H1RjgDc9GW.png
author: Rogers Kelamen
date: 2021-01-22 21:53:26
tags:
- MCM
categories:
- 工具
---

# 初识$\LaTeX$

> 首先你要知道LaTeX最初是作为**排版工具**被发明出来的

> warning：**标注有`*`的内容是专有的LaTeX环境，但md环境无法正确显示**

## 安装$\LaTeX$

> 首先LaTeX的安装还是有些麻烦的（主要是要安装编译器和TeX）

这里有一篇推荐教你如何安装的[知乎文章](https://zhuanlan.zhihu.com/p/56982388)

## 关于md支持$\LaTeX$的澄清

**首先并不是md文本支持$\LaTeX$排版，<u>而是md内置引擎支持$\LaTeX$的数学公式。</u>**

<font color=#34b>往细了说，就是md用`$`的书写方式制造的LaTeX其实是LaTeX的`displaymath`的环境（只是众多环境中的一种）。所以也就是说，在`displaymath`环境下能够书写的LaTeX格式，在md中就能展示（也只能展示这种环境）</font>

所以，打`*`的地方其实也可以理解为`displaymath`环境不支持。

## 空白距离*

空格和制表符等空白字符在LaTeX 中被看作相同的空白距离(space)。**多个连续**的空白字符等同于**一个空白字符**。在句首的空白距离一般会被忽略，单个空行也被认为是一个“空白距离”。

*两行文本间的空白行*标志着上段的结束和下段的开始。**多**个空白行的作用等同于**一个空白行**。

*PS：这里是专有的LaTeX环境下，但是md环境中的渲染引擎<u>无视一切空格</u>*

## 特殊字符

下面的这些字符是LATEX 中的**保留字符**(reserved characters)，它们或在LATEX 中 有特殊的意义，或不一定存在于所有字库中。

```latex
# $ % ^ & _ { }
```

在这些字符前加上反斜线，它们就可以正常的输出到文档中。(转义)

*ps：反斜线`\`不能通过在其前面加另一个反斜线得到`\\`；这是一个用来换行的命令*

## LaTeX命令

LATEX 命令(commands) 是大小写敏感的，有以下两种格式：

- 以一个反斜线(backslash) \ 开始，命令名只由字母组成。命令名后的空格 符、数字或任何非字母的字符都标志着该命令的结束。
- 由一个反斜线和非字母的字符组成。

warning\*：LATEX忽略命令之后的空白字符。如果你希望在命令后得到一个空格，可以 在命令后加上`{}`和一个空格，或加上一个特殊的空格命令。`{}` 将阻止LATEX吃掉命令后的所有空格。

```latex
\TeX{} \backslash
```

$$
\TeX{} \backslash
$$

### 可选参数

有些命令需要一个参数(parameter)，该参数用花括号(curly braces) { } 括 住并写在命令的后面。一些命令支持可选参数(optional parameters)，可选参数 可用方括号(square brackets) [ ] 括住。

```latex
\textsl{斜体}		% md无法显示
```

## 注释

LaTeX的注释和MATLAB居然都是一样的，都是用`%`。

*符号`%`也可以用来断开不能含有空白字符或换行符的较长输入内容。*

# 文档布局

## 文档类

当LATEX 处理源文件时，首先需要知道的就是作者所要创建的文档类型。文档类 型可由\documentclass 命令来指定。

```latex
\documentclass[options]{class}
```

**class 指定想要的文档类型。通过options 参数可以定制文档类的属性。**eg：

```latex
\documentclass[11pt,twoside,a4paper]{article}
```

这条命令会引导LATEX 使用article 格式、11 磅大小的字体来排版该文档，并得到在A4 纸上双面打印的效果。

| 文档类名（class） | 简单描述                                                     |
| ----------------- | ------------------------------------------------------------ |
| article           | 排版科学期刊、演示文档、短报告、程序文档、邀请函……           |
| proc              | 一个基于 article 的会议文集类。                              |
| minimal           | 非常小的文档类。只设置了页面尺寸和基本字体。主要用来查错。   |
| report            | 排版多章节长报告、短篇书籍、博士论文……                       |
| book              | 排版书籍。                                                   |
| slides            | 排版幻灯片。该文档类使用大号 sans serif 字体。也可以选用 FoilTEXa 来得到相同的效果。 |


| 文档类选项（option）        | 简单描述                                                     |
| --------------------------- | ------------------------------------------------------------ |
| 10pt, 11pt, 12pt            | 设置文档中所使用的字体的大小。如果该项没有指定，默认 使用10pt 字体。 |
| a4paper, letterpaper, . . . | 定义纸张的尺寸。缺省设置为letterpaper。此 外，还可以使用a5paper, b5paper, executivepaper 以及legalpaper。 |
| fleqn                       | 设置行间公式为左对齐，而不是居中对齐。                       |
| leqno                       | 设置行间公式的编号为左对齐，而不是右对齐。                   |
| titlepage, notitlepage      | 指定是否在文档标题(document title) 后另起一 页。article 文档类缺省设置为不开始新页，report 和book 类则相反。 |
| onecolumn, twocolumn        | LATEX 以单栏(one column) 或双栏(two column) 的 方式来排版文档。 |
| twoside, oneside            | 指定文档为双面或单面打印格式。article 和report 类 为单面(single sided) 格式，book 类缺省为双面(double sided) 格式。 |
| landscape                   | 将文档的打印输出布局设置为 landscape 模式。                  |
| openright, openany          | 决定新的一章仅在奇数页开始还是在下一页开始。在文 档类型为article 时该选项不起作用，因为该类中没有定义“章” (chapter)。report 类默认在下一页开始新一章而book 类的新一章总是在 奇数页开始。 |

## 宏包

排版文档时，你可能会发现某些时候基本的LATEX 并不能解决你的问题。如果想 插入图形(graphics)、彩色文本(coloured text) 或源代码到你的文档中，你就需要使用宏包来增强LATEX 的功能。可使用如下命令调用宏包

```latex
\usepackage[options]{package}
```

这里package 是宏包的名称，options 是用来激活宏包特殊功能的一组关键词。很 多宏包随LATEX 基本发行版一起发布（见下表）

| 宏包名（package） | 简单描述                                                     |
| ----------------- | ------------------------------------------------------------ |
| doc               | 排版LATEX 的说明文档。                                       |
| exscale           | 提供了按比例伸缩的数学扩展字体。                             |
| fontenc           | 指明使用哪种LATEX 字体编码(font encoding)。                  |
| ifthen            | 提供如下形式的命令 ‘if . . . then do . . . otherwise do . . . .’ |
| latexsym          | 提供LATEX 符号字体。                                         |
| makeidx           | 提供排版索引的命令。                                         |
| syntonly          | 编译文档而不生成 dvi 文件（常用于查错）。                    |
| inputenc          | 指明使用哪种输入编码，如 ASCII, ISO Latin-1, ISO Latin-2..... |

## 页面样式

LATEX 支持三种预定义的页眉/页脚(header/footer) 样式，称为页面样式(page style)。

```latex
\pagestyle{style}
```

| 页面样式名（style） | 简单描述                                     |
| ------------------- | -------------------------------------------- |
| plain               | 在页脚正中显示页码。这是页面样式的缺省设置。 |
| headings            | 在页眉中显示章节名及页码，页脚空白。         |
| empty               | 将页眉页脚都设为空白。                       |

 *可以通过如下命令来改变<u>当前页面</u>的页面样式：*

```latex
\thispagestyle{style}
```

## LaTeX文件

LaTeX有着非常多的不同的扩展名：

| 扩展名 | 简单描述                                                     |
| ------ | ------------------------------------------------------------ |
| .tex   | LATEX 或TEX 源文件。可以使用latex 命令编译。                 |
| .sty   | LATEX 宏包文件。可以使用\usepackage 命令将宏包文件载入到你的LATEX 文档中。 |
| .dtx   | 文档化TEX 文件。这是LATEX 宏包文件的主要发布格式。如果编译.dtx 文 档，将会得到其中包含的LATEX 宏包文件的文档化宏代码。 |
| .ins   | 对应.dtx 文件的安装文件。如果你从网上下载了一个LATEX 的宏包文件， 其中一般会包含一个.dtx 文件和一个.ins 文件。使用LATEX 处理.ins 文 件可以解开.dtx 文件。 |
| .cls   | 定义文档外观形式的类文件，可以通过使用\documentclass 命令选取。 |
| .fd    | 字体描述文件，可以告诉LATEX 有关新字体的信息。               |



下面是LaTeX编译源文件时产生的文件和扩展名：

| 扩展名 | 简单描述                                                     |
| ------ | ------------------------------------------------------------ |
| .dvi   | 设备无关文件。这是运行LATEX 编译的主要结果。你可以使用 DVI 预览器 预览其内容或使用dvips 或其他程序输出到打印机。 |
| .log   | 记录了上次编译时的详细信息。                                 |
| .toc   | 储存了所有的章节标题。下次编译时将读取该文件并生成目录。     |
| .lof   | 和.toc 文件类似，可生成图形目录。                            |
| .lot   | 和.toc 文件类似，可生成表格目录。                            |
| .aux   | 用来向下次编译传递信息的辅助文件。主要储存交叉引用的相关信息。 |
| .idx   | 如果文档中包含索引，LATEX 将使用该文件存储所有的索引词条。此文件 需要使用makeindex 处理 |
| .ind   | 处理过的.idx 文件。下次编译时将读入到你的文档中。            |
| .ilg   | 和.log 文件类似，记录了makeindex 命令运行的详细信息。        |

# 文本排版

## 文本和语言结构

LATEX 不同于其它排版系统之处在于，你必须告诉它文本的逻辑和语义结构。然后它根据类文件和各种样式文件中给定的“规则”生成相应格式的文本。

LATEX 最重要的文本单元（印刷术上的）是段落(paragraph)。我们称段落 为“文本单元”，因为段落是连续思想或者观点在排版上的反映。

## 断行和分页*

### 对齐段落

为了优化整个段落的内容，LATEX 在单词之间 插入必要的断行点(line break) 和间隙。如果一行的单词排不下，LATEX 也会进 行必要的断词。段落如何排版依赖于文档类别。通常，每一段的第一行有缩进， 在两段之间没有额外的间隔。

在特殊情形下，有必要命令LATEX 断行:

```latex
% 另起一行，而不另起一段:
\\ or \newline
% 在强制断行后，还禁止分页:
\\*
% 另起一页:
\newpage
```

### 断词

必要时LATEX 就会断词。如果断词算法不能确定正确的断词点，可以使用如下命 令告诉TEX 如何弥补这个缺憾：

```latex
\hyphenation{word list}
```

## 内置字符串

用来排版特殊文本字符串的一些非常简单的LATEX 命令:

| 命令      | 显示              |
| --------- | ----------------- |
| `\today`  | August 2, 2008(*) |
| `\TeX`    | $\TeX$            |
| `\LaTeX`  | $\LaTeX$          |
| `\LaTeXe` | $\LaTeX$2ε(*)     |

## 特殊字符和符号

### 引号*

你不能再像在打字机上那样，把” 用作引号(quotation marks)。在印刷中有专门 的左引号和右引号。在LATEX 中，用两个`（重音）产生左引号，用两个（' 直立引 号）产生右引号。一个‘ 和一个’ 产生一个单引号。

```latex
‘‘Please press the ‘x’ key.’’
```

$$
‘‘Please press the ‘x’ key.’’
$$

### 破折号和连字号*

LATEX 中有四种短划(dash) 标点符号。连续用不同数目的短划，可以得到其中的 三种。第四个实际不是标点符号，它是数学中的减号：

```latex
daughter-in-law, X-rated\\
pages 13--67\\
yes---or no? \\
$0$, $1$ and $-1$
```

```text
daughter-in-law, X-rated
pages 13–67
yes——or no?
0, 1 and −1
```

### 波浪号(∼)*

波浪号经常和网址用在一起。它在LATEX 中，可用\~ 产生，但其结果：˜ 却不是

```latex
http://www.rich.edu/\~{}bush
```

```text
http://www.rich.edu/˜bush
```

### 度的符号(◦)

下面的例子演示了在LATEX 中如何排版度的符号(degree symbol)：

```latex
It’s $-30\,^{\circ}\mathrm{C}$.
```

It’s $-30\,^{\circ}\mathrm{C}$.

*textcomp 宏包里有另外一个度的符号`\textcelsius`。*

### 省略号(. . . )

在打字机上，逗号(comma) 或句号(period) 占据的空间和其他字母相等。**在书籍印刷中，这些字符仅占据一点儿空间**。所以不 能只键入三个点来输出“省略号”(ellipsis)，因为间隔划分得不对。有一个专门 的命令输出省略号。它被称为:

```latex
\ldots
```

效果：$\ldots$

### 连字*

一些字母组合不是简单键入一个个字母得到得的，而实际上用到了一些特殊符号。

在两个字母之间插入一个`\mbox{}`，可以禁止连字。

### 注音符号和特殊字符

LATEX 支持来自许多语言中的注音符号(accent) 和特殊字符(special character)。

**在字母 i 和 j 上标一个注音符号，它的点儿必须去掉。这个可由`\i` 和`\j`做 到。**

![](https://s2.loli.net/2022/03/26/7MzUG2ySR8g43rB.png)

## 标题、章和节*

> 为便于读者理解，应该把文档划分为章，节和子节。LATEX 用专门的命令支持 这个工作，这些命令把节的标题作为参量。

对article 风格的文档，有下列分节命令：

```latex
\section{...}
\subsection{...}
\subsubsection{...}
\paragraph{...}
\subparagraph{...}
```

如果想把文档分成几个部分而且不影响章节编号，你可以使用:

```latex
\part{...}
```

当你使用report 或者book 类的时候，可以用另外一个高层次的分节命令:

```latex
\chapter{...}
```

整篇文档的标题(title) 由命令:

```latex
\maketitle
```

标题的内容必须在调用\maketitle 以前，由命令:

```latex
\title{...}, \author{...} 和可选的\date{...}
```

## 交叉引用*

在书籍、报告和论文中，需要对图、表和文本的特殊段落进行交叉引用(crossreferences)。LATEX 提供了如下交叉引用命令:

```latex
\label{marker}, \ref{marker} 和\pageref{marker}
```

*其中marker 是用户选择的标识符。*

## 脚注*

```latex
\footnote{footnote text}
```

eg：

```latex
Footnotes\footnote{This is a footnote.} are often used by people using \LaTeX.
```

## 强调*

如果文本是用打字机键入的，<u>用下划线</u>来强调重要的单词。

```latex
\underline{text}
```

但是在印刷的书中，用一种斜体字体排印要强调的单词。LATEX 提供命令:

```latex
\emph{text}
```

## 环境(environment)

为了排版专用的文本，LATEX 定义了各种不同格式的环境(environment)：

```latex
\begin{environment} text \end{environment}
```

其中environment 是环境的名称。只要保持调用顺序，环境可以嵌套。

```latex
\begin{aaa}...\begin{bbb}...\end{bbb}...\end{aaa}
```

### Itemize、Enumerate、Description*

itemize 环境适用于简单的列表，enumerate 环境适用于有排列序号的列表， 而description 环境用于带描述的列表。

```latex
\flushleft
\begin{enumerate}
\item You can mix the list
environments to your taste:
\begin{itemize}
\item But it might start to
look silly.
\item[-] With a dash.
\end{itemize}
\item Therefore remember:
\begin{description}
\item[Stupid] things will not
become smart because they are
in a list.
\item[Smart] things, though,
can be presented beautifully
in a list.
\end{description}
\end{enumerate}
```

效果：

```text
1. You can mix the list environments to your taste:
	• But it might start to look silly.
	- With a dash.
2. Therefore remember:
	Stupid things will not become smart because they are 	 in a list.
	Smart things, though, can be
	presented beautifully in a list.
```

###左对齐、右对齐和居中*

flushleft 和flushright 环境分别产生左对齐(left-aligned) 和右对齐(rightaligned) 的段落。center 环境产生居中的文本。

```latex
\begin{flushleft}
This text is\\ left-aligned.
\LaTeX{} is not trying to make
each line the same length.
\end{flushleft}

\begin{flushright}
This text is right-\\aligned.
\LaTeX{} is not trying to make
each line the same length.
\end{flushright}

\begin{center}
At the centre\\of the earth
\end{center}
```

### 引用、语录和韵文*

quote 环境可以用于引文、语录和例子:

```latex
A typographical rule of thumb
for the line length is:
\begin{quote}
On average, no line should
be longer than 66 characters.
\end{quote}
This is why \LaTeX{} pages have
such large borders by default
and also why multicolumn print
is used in newspapers.
```

*有两个类似的环境：quotation 和verse 环境。quotation 环境用于超过 几段的较长引用，因为它对段落进行缩进。verse 环境用于诗歌，在诗歌中断行 很重要。在一行的末尾用`\\` 断行，在每一段后留一空行。*

### 摘要*

一般abstract 用于 article 类文档。

```latex
\begin{abstract}
The abstract abstract.
\end{abstract}
```

# 数学公式

> 在这里才是$\TeX$真正强大的地方。

LATEX 使用一种特有的模式来排版数学(mathematics) 公式。数学公式允许以 行间形式排版在一个段落之中，也可以以独立形式排版，此时段落可能会被拆 开。处于段内的数学文本要放在`\(` 与`\)` 之间，$ 与$ 之间，或者`\begin{math} `与`\end{math}` 之间。

```latex
Add $a$ squared and $b$ squared
to get $c$ squared. Or, using
a more mathematical approach:
$c^{2}=a^{2}+b^{2}$
```

Add $a$ squared and $b$ squared to get $c$ squared. Or, using a more mathematical approach: $c^{2}=a^{2}+b^{2}$

当你希望把自己的一些较长的数学方程或是公式单独的放在段落之外的时 候，那么你最好显示 (display) 它们，而不要拆开此段落。为此，你可以把它们 放在`\[`与`\]`之间，或者`\begin{displaymath}` 与`\end{displaymath}` 之间。(*<font color=brown>md默认块就是`displaymath`环境</font>*)

```latex
Add $a$ squared and $b$ squared
to get $c$ squared. Or, using
a more mathematical approach:
\begin{displaymath}
c^{2}=a^{2}+b^{2}
\end{displaymath}
or you can type less with:
\[a+b=c\]
```

如果你希望LATEX 给你的方程编上号，你可以使用equation 环境。然后你 就可以用\label 来给一个方程加上标签并在文中的某处用\ref 或amsmath 宏 包中的\eqref 命令来引用它。

```latex
\begin{equation} \label{eq:eps}
\epsilon > 0
\end{equation}
From (\ref{eq:eps}), we gather
\ldots{}From \eqref{eq:eps} we
do the same.
```

注意一下公式排版样式的不同，`$`是行间式样，`\begin{displaymath}`是显示式样。

## 数学模式的群组

大部分数学模式的命令只对其后的一个字符有效，因此，如果你希望一个命令对 多个字符起作用，你必须把它们放在一个群组中，使用花括号：{...}.

```latex
\begin{equation}
a^x+y \neq a^{x+y}
\end{equation}
```

## 数学公式的基本元素(!)

> **即将介绍数学排版中的<u>最重要的一些命令</u>**，也是md中最重要的
>
> 你可以先记住`\`是一个神奇的符号

**小写希腊字母** (Greek letters) 的输入为`\alpha`、`\beta`、`\gamma`……，大 写字母的输入为`\Gamma`、`\Delta`.......

**指数和下标**可以能过使用`^` 和`_`两个符号来指定。

**平方根** (square root) 输入用`\sqrt`；n 次根用`\sqrt[n]` 来得到。根号的大小由LATEX自动决定。如果仅仅需要根号，可以用`\surd` 得到。



命令`\overline` 和`\underline` 产生**水平线**，它们会被放在表达式的正上方 或是正下方。

```latex
$\overline{m+n}$
```

$$
\overline{m+n}
$$



命令`\overbrace` 和`\underbrace`可以在一个表达式的上方或下方生成**水平括号**

```latex
$\underbrace{a+b+\cdots+z}_{26}$
```

$$
\underbrace{a+b+\cdots+z}_{26}
$$



为了给变量增加数学重音符号，如小箭头或是˜(tilde)，可以用之后的命令。覆盖多个字符的宽“帽子”和宽˜号，可以由`\widehat` 和`\widetilde` 得到。`’`符号则给出了一个撇号(prime)。

```latex
\begin{displaymath}
y=x^{2}\qquad y’=2x\qquad y’’=2
\end{displaymath}
```

$$
y=x^{2}\qquad y’=2x\qquad y’’=2
$$



**向量**可以通过在一个变量上方添加小箭头(arrow symbols) 来指定。为此， 使用`\vec` 命令即可。`\overrightarrow` 和`\overleftarrow` 这两个命令可以用来表示一个从 A 到 B 的向量。

```latex
\begin{displaymath}
\vec a\quad\overrightarrow{AB}
\end{displaymath}
```

$$
\vec a\quad\overrightarrow{AB}
$$



通常你没有必要打出一个明显的**点号**(用来表示乘法)来表明乘法运算；但是有时候也需要它来帮助读者分清一个公式。在这些情况下，你应该使用`\cdot` 命令。

```latex
\begin{displaymath}
v = {\sigma}_1 \cdot {\sigma}_2
{\tau}_1 \cdot {\tau}_2
\end{displaymath}
```

$$
v = {\sigma}_1 \cdot {\sigma}_2
{\tau}_1 \cdot {\tau}_2
$$



log 等类似的函数名通常是用直立字体，而不是如同变量一样用斜体，因 此LATEX 提供了以下的命令来排版这些最重要的函数名：

![](https://s2.loli.net/2022/03/26/3axNrJAjDWL5mXT.png)

eg：

```latex
\lim_{x \rightarrow 0}
\frac{\sin x}{x}=1
```

$$
\lim_{x \rightarrow 0}
\frac{\sin x}{x}=1
$$



对于**取模函数**(modulo function)，有两个命令：`\bmod` 用于二元运算“`a mod b`”，而`\pmod` 则用于表达式如“`x ≡ a (mod b)`”。

一个上下的**分式**(fraction) 可用`\frac`{...}{...} 命令得到。而其倾斜形式如 1/2，有时是更好的选择，因为对于简短的分子分母来说，这看上去更美观。

```latex
\begin{displaymath}
\frac{ x^{2} }{ k+1 }\qquad
x^{ \frac{2}{k+1} }\qquad
x^{ 1/2 }
\end{displaymath}
```

$$
\frac{ x^{2} }{ k+1 }\qquad
x^{ \frac{2}{k+1} }\qquad
x^{ 1/2 }
$$



排版**二项式系数**或类似的结构，你可以使用amsmath 宏包中的`\binom` 命 令。

```latex
\begin{displaymath}
\binom{n}{k}\qquad\mathrm{C}_n^k
\end{displaymath}
```

$$
\binom{n}{k}\qquad\mathrm{C}_n^k
$$



有时候你需要把符号互相堆积起来。`\stackrel`命令会把其第一个参数中的符号以上标大小放在第二个上面，而第二个符号则以正常的位置摆放。

```latex
\begin{displaymath}
\int f_N(x) \stackrel{!}{=} 1
\end{displaymath}
```

$$
\int f_N(x) \stackrel{!}{=} 1
$$



**积分号** (integral operator) 可以用`\int` 产生，**求和号** (sum operator) 用`\sum` 命令，而**乘积号** (product operator) 要用`\prod` 命令。上限和下限用`^` 和`_`来指定，如同上标与下标一样

```latex
\begin{displaymath}
\sum_{i=1}^{n} \qquad
\int_{0}^{\frac{\pi}{2}} \qquad
\prod_\epsilon
\end{displaymath}
```

$$
\sum_{i=1}^{n} \qquad
\int_{0}^{\frac{\pi}{2}} \qquad
\prod_\epsilon
$$



为了更好的控制一个复杂表达式中指标的放置，amsmath 提供了两个额外 的工具：`\substack` 命令和`subarray` 环境：

```latex
\begin{displaymath}
\sum_{\substack{0<i<n \\ 1<j<m}}
P(i,j) =
\sum_{\begin{subarray}{l}
i\in I\\
1<j<m
\end{subarray}} Q(i,j)
\end{displaymath}
```

$$
\sum_{\substack{0<i<n \\ 1<j<m}}
P(i,j) =
\sum_{\begin{subarray}{l}
i\in I\\
1<j<m
\end{subarray}} Q(i,j)
$$



TEX提供了各种各样的符号来得到括号 (braces) 和其他定界符(delimiters)。**圆括号和方括号可以由对应的键直接输入而花括号要用`\{`，**但是所有其它的定界符都要用一定的命令(如：\updownarrow) 生成。

```latex
\begin{displaymath}
{a,b,c}\neq\{a,b,c\}
\end{displaymath}
```

$$
{a,b,c}\neq\{a,b,c\}
$$



如果你在某个左定界符前放一个`\left` 命令或是在某个右定界符前放一 个`\right` 命令，TEX 将会自动决定这对定界符的大小。**请注意，你必须为每个`\left` 命令配对相应的`\right` 命令**，而且只有在左右定界符被排版在同一行时才会获得正确的大小尺寸。

```latex
\begin{displaymath}
1 + \left( \frac{1}{ 1-x^{2} } \right) ^3
\end{displaymath}
```

$$
1 + \left( \frac{1}{ 1-x^{2} } \right) ^3
$$



有些情况下，有必要手工指定一个数学定界符的正确尺寸，这可以使 用`\big`，`\Big`，`\bigg` 和`\Bigg` 命令，大多数情况下你只需把它们放在定界符 命令的前面

```latex
$\Big( (x+1) (x-1) \Big) ^{2}$\\
$\big(\Big(\bigg(\Bigg($\quad
$\big\}\Big\}\bigg\}\Bigg\}$
\quad
$\big\|\Big\|\bigg\|\Bigg\|$
```

$$
\Big( (x+1) (x-1) \Big) ^{2}\\
\big(\Big(\bigg(\Bigg(\quad
\big\}\Big\}\bigg\}\Bigg\}
\quad
\big\|\Big\|\bigg\|\Bigg\|
$$



有很多命令可以实现在公式中插入**三点列** (three dots)。\ldots 得到在基 线上的点列而\cdots 是上下居中的点列。另外，还有\vdots 命令产生竖直的点 列，\ddots 产生对角线的点列。

```latex
\begin{displaymath}
x_{1},\ldots,x_{n} \qquad
x_{1}+\cdots+x_{n}
\end{displaymath}
```

$$
x_{1},\ldots,x_{n} \qquad
x_{1}+\cdots+x_{n}
$$



## 数学空格

可以通过插入一些特殊的空格控制命令来调整自定义空格

| 命令     | 空格大小             |
| -------- | -------------------- |
| `\,`     | $\frac{3}{18}$ quad  |
| `\:`     | $\frac{4}{18}$ quad  |
| `\`      | 中等大小             |
| `\;`     | $\frac{5}{18}$ quad  |
| `\quad`  | 1 quad               |
| `\qquad` | 2 quad               |
| `\!`     | -$\frac{3}{18}$ quad |

## 垂直取齐

要排版数组，使用`array` 环境。它的使用与`tabular` 环境有些类似。`\\` 命令可用来断行。

```latex
\begin{displaymath}
\mathbf{X} =
\left( \begin{array}{ccc}
x_{11} & x_{12} & \ldots \\
x_{21} & x_{22} & \ldots \\
\vdots & \vdots & \ddots
\end{array} \right)
\end{displaymath}
```

$$
\mathbf{X} =
\left( \begin{array}{ccc}
x_{11} & x_{12} & \ldots \\
x_{21} & x_{22} & \ldots \\
\vdots & \vdots & \ddots
\end{array} \right)
$$

`array` 环境也可以用来排版这样的表达式，表达式中使用一个“`.`” 作为其隐 藏的`\right` 定界符。

```latex
\begin{displaymath}
y = \left\{ \begin{array}{ll}
a & \textrm{if $d>c$}\\
b+x & \textrm{in the morning}\\
l & \textrm{all day long}
\end{array} \right.
\end{displaymath}
```

$$
y = \left\{ \begin{array}{ll}
a & \textrm{if $d>c$}\\
b+x & \textrm{in the morning}\\
l & \textrm{all day long}
\end{array} \right.
$$



## 虚位

我们看不见虚位（phantom，也有幻影的意思），但是在许多人的头脑中它们依然 占有一定的位置。

使用`\phantom` 命令你可以给不在最终输出中显示的字符保留位置。*理解此意的最 好方法是看下面的例子。*

```latex
\begin{displaymath}
{}^{12}_{\phantom{1}6}\textrm{C}
\qquad \textrm{versus} \qquad
{}^{12}_{6}\textrm{C}

\\

\Gamma_{ij}^{\phantom{ij}k}
\qquad \textrm{versus} \qquad
\Gamma_{ij}^{k}
\end{displaymath}
```

$$
{}^{12}_{\phantom{1}6}\textrm{C}
\qquad \textrm{versus} \qquad
{}^{12}_{6}\textrm{C}
\\
\Gamma_{ij}^{\phantom{ij}k}
\qquad \textrm{versus} \qquad
\Gamma_{ij}^{k}
$$



## 数学符号表

> LaTeX中的数学符号实在太多了，所以列出表就很有必要

![](https://s2.loli.net/2022/03/26/F3iA1QB9MKa8Gmd.png)

![](https://s2.loli.net/2022/03/26/SIcxG2zdoJ4yBwr.png)

![](https://s2.loli.net/2022/03/26/SAaz3imGwnDNHK2.png)

![](https://s2.loli.net/2022/03/26/OKZQg2Ax3uywUIY.png)

![](https://s2.loli.net/2022/03/26/9h6vwXndPsRMc47.png)

![](https://s2.loli.net/2022/03/26/a5lCuRx1BFc9ihY.png)

![](https://s2.loli.net/2022/03/26/5Pe3GFAYabJrVzQ.png)

![](https://s2.loli.net/2022/03/26/Jz2hZX3ID1NGxY5.png)

![](https://s2.loli.net/2022/03/26/FMiVaudl3frXmTz.png)

![](https://s2.loli.net/2022/03/26/dXoJC2YVcSMfk7l.png)

![](https://s2.loli.net/2022/03/26/TtJh9o3GxcZQ5Xp.png)

![](https://s2.loli.net/2022/03/26/lFQuJSUw7Iyx9Zi.png)

# Link
本教程基于[这本书](https://pan.baidu.com/s/1kVhKR4G1vtzDWJ1ex8GYgQ) (*提取码：2nw4*)
