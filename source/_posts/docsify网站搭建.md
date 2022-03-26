---
title: docsify网站搭建
index_img: https://s2.loli.net/2022/03/26/ODQxPEIvrLKXoYS.png
date: 2020-11-08 21:34:36
author: Rogers Kelamen
updated: 2020-11-10 20:50:00
categories:
- 网站
tags:
- docsify
---



# docsify网站搭建教程（已完结）

## 写在前头

> 有人会说了：搞什么搞什么！你这个网站不是用hexo搭的么！？
> 好吧我承认这个博客是用hexo搭建的 ( •̀ ω •́ )✧，但是不得不说hexo的坑实在是太多了，我曾一度想要放弃这个hexo的网站而只去捣鼓docsify╰(*°▽°*)╯。鉴于我对docsify的经验了解（反正我对hexo的了解还很少），再加上有朋友要求我写一个docsify的教程（话说[官方文档](https://docsify.js.org/#/)有那么难看懂吗），于是我就写下这篇文章（可能会断更），希望帮助一些小伙伴少踩一些坑。

*ps：我这篇教程还是基于[官方文档](https://docsify.js.org/#/)的，所以索引顺序啥的还是会按照官方来。但是，本教程并非按官方照抄不误，有些官方的解释说明在教程中不会重复提及，本教程秉持着对于官方的补充以及对官方中一些坑的解决。当然，要是觉得我讲解的太low了，可以直接去看[官方文档](https://docsify.js.org/#/)。*

# Quick Start

> 还是先看看官方怎么介绍docsify的吧：docsify 可以快速帮你生成文档网站。不同于 GitBook、Hexo 的地方是它不会生成静态的 `.html` 文件，所有转换工作都是在运行时。如果你想要开始使用它，只需要创建一个 `index.html` 就可以开始编写文档并直接[部署在 GitHub Pages](https://docsify.js.org/#/zh-cn/deploy)。

*其实官网上还介绍了它的一些特性，有兴趣的可以去看看。*

## 即刻开始

安装docsify-cli工具

```bash
npm i docsify-cli -g
```

*ps：*

- *这里使用npm安装，所以你首先要有安装node.js*
- *`-g`是全局安装（推荐），你也可以在单个项目中安装docsify-cli工具*

## clone项目

**从github上拉取你的用来搭网站的仓库到本地**

```bash
git clone ...
```

然后你得到了你的本地仓库，譬如名如yourname.github.io（<font color=brown>这个仓库必须要是以.github.io结尾的</font>）的仓库，接下来就是初始化的步骤了。

## 初始化

直接在这个仓库下输入初始化指令

```bash
docsify init ./docs
```

这样会在根目录下创建一个名为docs的文件夹，**下面会自动生成一些文件（都不要删）**

然后命令行会有如下提示
![](https://s2.loli.net/2022/03/26/Ekah2dqw9nQmo7A.png)

```bash
docsify serve docs
```

这样就可以实时查看预览效果。

## Others

在docs文件夹下的index.html中

```html
<!-- index.html -->
<div data-app id="main">加载中</div>

<script>
    window.$docsify = {
        el: '#main'
    }
</script>
```

`window.$docsify`对象就是我们将来的配置项的重要对象。

# Write Docs

> 浅谈一下docsify的基本原理

好吧也没什么高深的，就是在`index.html`的

```html
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```

也就是说docsify的精髓就是在这个docsify.min.js这个文件中，<u>有了这个文件docsify的强大功能和配置才能有效果。</u>

## 多页文档

>  我们写出来的文档肯定必须是要有层次和结构的，即得是多页文档。
>  如果是多页文档，那咱们就得定制一个有层级结构的侧边栏。

*其实[官方文档](https://docsify.js.org/#/)已经把结构讲的很详细了，即*

```text
/
└── docs
    ├── README.md
    ├── guide.md
    └── zh-cn
        ├── README.md
        └── guide.md
```

那么docs文件夹下的`docs/README.md`就是/#/展示的内容，而`docs/guide.md`就是/#/guide展示的内容，`docs/zh-cn/READMD.md`就是/#/zh-cn/展示的内容。

**这一部分要深刻理解，因为要是没有对docsify的层级结构的清楚认识，我们就会不知道怎么将这一个个文档正确归类来形成一个清晰的结构（不管是对读者还是作者，会造成阅读困难）**

我个人是这么安排的：**<font color=#34b>每个README.md就设置成这一个部分或版块的介绍页，因为点击一个版块的时候是默认跳转到READ.md文件，所以正好就可以直接跳转到这个部分的介绍或简介。</font>**

## 定制侧边栏

定制侧边栏很简单，只需在同级的版块中创建一个`_sidebar.md`（*由此我们可以知道docsify的文件几乎就是由md文件组成*），然后再在`index.html`中开启

```html
<script>
  window.$docsify = {
    loadSidebar: true
  }
</script>
```

`_sidebar.md`的格式如下：

```markdown
* [首页](/)
* [指南](/guide)
```

**于是乎你点击侧边栏的首页就会跳转到`/zh-cn/READMD.md`的内容中去，点指南就会跳转到`/zh-cn/guide.md`的内容中去。（请一定要把这个关系理清楚！）**

## 嵌套侧边栏

> 说实话我都不知道官方在这部分在说啥，感觉有点没讲到点子上

说到底就是你每点击一个子级版块docsify会自动跳转到这个版块的README.md（要是我的话就是这个板块的介绍页），此时侧边栏也会跳转，会变成这个版块中的`_sidebar.md`中的层级内容。

```text
/
└── docs
    ├── README.md
    ├── _sidebar.md
    ├── guide.md
    └── zh-cn
        ├── README.md
        ├── _sidebar.md
        └── guide.md
```

**<font color=#34b>也就是说你点击了zh-cn所在的锚点，docsify就会跳转到/zh-cn/README.md，然后侧边栏就会改为加载/zh-cn/_sidebar.md</font>**

### 一个坑

>  在多级的`_sidebar.md`中，我们可能会不知道怎么去设置文件中的链接（*反正我是踩进坑了的*）

假如我有一个第三级的_sidebar.md，其中的内容就该这么写

```markdown
* [介绍](/zh-cn/son)
* [第一个内容](/zh-cn/son/content)
```

*反正一定要把层级结构理清楚*

### 又一个坑

> 当我们最开始接触`_sidebar.md`中的链接时，有可能会感到疑惑，不知道它自己的url链接规则

其实docsify的url是用一个起始hash锚点来规划的，先是一个`#`，然后再是我们要写的链接，eg：

```text
(/zh-cn/)
```

这个链接就会转到<u>yourname.github.io/#/zh-cn/</u>

# 导航栏

## 通过HTML

> 也没啥多说的：就像官方给的那样增加`<nav>`标签

```html
<!-- index.html -->
<body>
  <nav>
    <a href="#/">EN</a>
    <a href="#/zh-cn/">中文</a>
  </nav>
  <div id="app"></div>
</body>
```

还是要提一嘴：*这里的链接和之前的都不一样，是以`#/`开头的*

## 通过配置文件

> 这里的配置方法官方已经讲的很详细了，我在这里就不再重复了。
> 为什么不重复（lian dei shuo）了，因为这种方法感觉效果和html差不多，但是步骤又有点麻烦了。

> 嵌套啥的也不说了吧，反正也很简单，官方也讲的很详细了
> 这里就放个图意思意思吧。

![](https://docsify.js.org/_images/zh-cn/nested-navbar.png)

*但是要注意的是：嵌套式的导航必须得是用配置文件配置*

# CoverPage

> 终于要讲封面了，毕竟这个docsify的美化效果基本上就是这个封面来撑场子了o(￣▽￣)ｄ

## basic usage

得先在`index.html`中设置配置项：

```html
<!-- index.html -->

<script>
  window.$docsify = {
    coverpage: true
  }
</script>
<script src="//cdn.jsdelivr.net/npm/docsify/lib/docsify.min.js"></script>
```

然后就可以在`_coverpage.md`中写入你想要的内容：
*这里官网已经讲的很好了，其中的代码也写得很详细，直接按照官网的样子来就好了。*

# 主题（theme）

> 第二个对美化有撑场子作用的东西（这里我特意更改了官方顺序）

**怎么用咧？**

```html
<!-- index.html -->
<head>
    <link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css">
</head>
```

***这个link就是你应用的主题的地址，当你修改其中的href值就可以更换主题了。***

目前`docsify`官方中列出来的所有支持主题和预览效果如下：

`Vue`（默认主题）：`<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/vue.css">`
![](https://s2.loli.net/2022/03/26/LQKkOdS1jwq3WfE.png)`Buble`：`<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/buble.css">` ![](https://s2.loli.net/2022/03/26/cXsRPjrbvFCfy8V.png)

`Dark`：`<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/dark.css">`

![](https://s2.loli.net/2022/03/26/7hdTiwOtgBPZKeb.png)

`Pure`：`<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/pure.css">`

![](https://s2.loli.net/2022/03/26/DxBX8aSKprLQvPH.png)

`Dolphin`：`<link rel="stylesheet" href="//cdn.jsdelivr.net/npm/docsify/themes/dolphin.css">`

![](https://s2.loli.net/2022/03/26/VXCKiakgWvZhMJH.png)

`Docsify-Themeable-Default`：`<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-defaults.css">`

![](https://s2.loli.net/2022/03/26/aYSKRd8ADev7Uqf.png)

`Docsify-Themeable-Sample`：`<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple.css">`

![](https://s2.loli.net/2022/03/26/3zju4a9NFKcrUCo.png)

`Docsify-Themeable-Sample-Dark`：`<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/docsify-themeable@0/dist/css/theme-simple-dark.css">`

![](https://s2.loli.net/2022/03/26/FmEYqihetOQ4Xsu.png)

*主题不多，且用且珍惜吧（或者你可以自己写一个）*

# 配置项

> 还是那句话，[官方文档](https://docsify.js.org/#/)中的配置项写得详细且全面，建议直接去看官方的说明。我在本教程中只介绍几个我用上的配置项（个人认为比较常用的），以便缩短你的阅读时间。

## [el](https://docsify.js.org/#/zh-cn/configuration?id=el)

docsify 初始化的挂载元素，可以是一个 CSS 选择器，默认为 `#app` 如果不存在就直接绑定在 `body` 上。

*初始化项目之后最初就会有的配置项，默认是`'#app'`,即绑定了`div#app`这个元素。*

## [repo](https://docsify.js.org/#/zh-cn/configuration?id=repo)

配置仓库地址或者 `username/repo` 的字符串，会在页面右上角渲染一个 [GitHub Corner](http://tholman.com/github-corners/) 挂件。

*这个repo建议配置（能够美化你的docsify，配置后会在coverpage上显示Github挂件。*

```js
window.$docsify = {
  repo: 'docsifyjs/docsify',
  // or
  repo: 'https://github.com/yourname/yourrepo/',
};
```

## [maxLevel](https://docsify.js.org/#/zh-cn/configuration?id=maxlevel)

默认情况下会抓取文档中所有标题渲染成目录，可配置最大支持渲染的标题层级。

```js
window.$docsify = {
  maxLevel: 4,
};
```

*因为这个的<u>默认值是6</u>，所以要是不进行设置的话，可能会对侧边栏层级结构造成影响进而影响美观，设置为较小的值则对侧边栏的展开效果会更好（比如4）*

## [relativePath](https://docsify.js.org/#/zh-cn/configuration?id=relativepath)

如果该选项设为 **true** ，那么链接会使用相对路径。

```js
window.$docsify = {
  // 启用相对路径
  relativePath: true,

  // 禁用相对路径（默认值）
  // relativePath: false,
};
```



**将必须写成绝对路径改成允许写入相对路径并可识别，这种好处就不用多说了吧||ヽ(*￣▽￣*)ノ**

eg：*我有一个三个层级的目录结构，然后我要在`_sidebar.md`中写绝对路径，可能需要这样：*

```markdown
* [爬虫](/zh-cn/notes/python/spider)
```

*但是我用绝对路径只需要这样：*

```markdown
* [爬虫](spider)
```

**当多个层级中有多个文档时，相对路径简直不要太香。**

## [name](https://docsify.js.org/#/zh-cn/configuration?id=name)

文档标题，会显示在侧边栏顶部。

```js
window.$docsify = {
  name: 'docsify',
};
```

*这个可以用来展示你的大名，配合`nameLink`之后点击这个名字之后跳转到docsify的首页。*

## [logo](https://docsify.js.org/#/zh-cn/configuration?id=logo)

> 可以和name配置项配套使用，也可以单独使用，也相当于是展示个人信息的方式

在侧边栏中出现的网站图标，你可以使用`CSS`来更改大小

```js
window.$docsify = {
  logo: '/_media/icon.svg',
};
```

*其实我还不知道怎么用CSS更改大小，知道的大佬还请在评论区指教我一下*

## [nameLink](https://docsify.js.org/#/zh-cn/configuration?id=namelink)

点击文档标题`name`后跳转的链接地址。

```js
window.$docsify = {
  nameLink: '/',

  // 按照路由切换
  nameLink: {
    '/zh-cn/': '/zh-cn/',
    '/': '/',
  },
};
```

*就是和`name`配置项配合使用的。*

## [routerMode](https://docsify.js.org/#/zh-cn/configuration?id=routermode)

可以用这个配置项更改url的路由模式（默认的是hash）

```js
window.$docsify = {
  routerMode: 'history', // default: 'hash'
};
```

*其实我也不太清楚history和hash路由的原理区别，我知道的是改用history模式的话url就不会有hash路由特有的`#`，而是直接是`/.../...`这种样子。*

ps：<font color=#34b>要是改成了history，因为没有了`#`，有些在链接中写入了`#`的地方就得修改一下（去掉`#`）</font>

## [notFoundPage](https://docsify.js.org/#/zh-cn/configuration?id=notfoundpage)

在找不到指定页面时加载`_404.md`:

```js
window.$docsify = {
  notFoundPage: true,
};
```

加载自定义404页面:

```js
window.$docsify = {
  notFoundPage: 'my404.md',
};
```

# 代码高亮

> **docsify**内置的代码高亮工具是 [Prism](https://github.com/PrismJS/prism)。Prism 默认支持的语言基本有：markup，html，xml，svg，mathml，css，js（反正其他的我也不认识😜）。说到底，它默认支持的语言还是很有限的（python啥的后端语言都没高亮），于是我们需要额外的代码高亮。

[添加额外的语法支持](https://prismjs.com/#supported-languages)需要通过CDN添加相应的[语法文件](https://cdn.jsdelivr.net/npm/prismjs@1/components/)来配置高亮显示，eg：

```html
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-bash.min.js"></script>
<script src="//cdn.jsdelivr.net/npm/prismjs@1/components/prism-php.min.js"></script>
```

这样我们就有了bash和php的语法高亮。

*<font color=brown>ps：这些语法文件和docsify的库文件一样，都得放到body中，而不是head标签。（我反正之前有掉到坑里）</font>*

# Plugins

> 鉴于插件这个部分的安装都很简单（只需要引入文件并修改配置项就行），而且[官方文档](https://docsify.js.org/#/zh-cn/plugins)讲得都还不错，我在这里就不细细介绍了，就只对几个我认为比较好比较必要的插件做一个推荐。

- ## [全文搜索 - Search](https://docsify.js.org/#/zh-cn/plugins?id=全文搜索-search)

  会在侧边栏中出现一个搜索框，实现一个还不错的全局搜索功能。但是配置项还有一点麻烦。

- ## [复制到剪贴板](https://docsify.js.org/#/zh-cn/plugins?id=复制到剪贴板)

  顾名思义，实现复制markdown中代码的功能。

  详情可参考 [README.md](https://github.com/jperasmus/docsify-copy-code/blob/master/README.md) 。

- ## [Pagination](https://docsify.js.org/#/zh-cn/plugins?id=pagination)

  docsify的一个分页导航插件。

- ## [字数统计](https://docsify.js.org/#/zh-cn/plugins?id=字数统计)

  它提供了统计中文汉字和英文单词的功能，并且排除了一些markdown语法的特殊字符例如*、-等

  相关配置项可参考[document](https://github.com/827652549/docsify-count)。

  *这个插件的阅读时长的估计感觉很离谱，目前我还没找到解决的方法。。*

- ## [emoji](https://docsify.js.org/#/zh-cn/plugins?id=emoji)

  默认是提供 emoji 解析的，​但是它不是精准的，因为没有处理非 emoji 的字符串。如果你需要正确解析 emoji 字符串，你可以引入这个插件。

- ## [图片缩放 - Zoom image](https://docsify.js.org/#/zh-cn/plugins?id=图片缩放-zoom-image)

  顾名思义，实现图片查看和图片缩放功能。

- ## [Gitalk](https://docsify.js.org/#/zh-cn/plugins?id=gitalk)

  > 如果你想要你的docsify网站拥有评论功能，你可以安装这个插件

  这个插件是一个现代化的，基于Preact和Github Issue的评论系统。（相信很多人都用过Github Issue评论系统并在里面发布过评论吧）

  *ps：有可能会不稳定，或者是同步不及时的情况，可以酌情考虑安装。*

  *注意：docsify还提供了[Disqus](https://docsify.js.org/#/zh-cn/plugins?id=disqus)评论系统。但是为什么我没有介绍呢，很简单——这玩意需要翻墙。*

# 小白向（初识git）

> 如果你很熟悉git操作，那么你不用往下看了（相当于这篇教程你已经看完了）；如果你完全不知道怎么用git推代码，或者你会问我git是神马东西，你可以选择往下看了（或者在网上随便找一篇写得好的git入门文章）

你可能会问了：我这都搞了这么半天了，但是还是只能在本地上看到我的文档网站，我怎么通过网络访问到我的docsify网站呢？

A：使用git。

步骤：

1. 在你用于编辑代码的编辑器中（不管你是vscode还是sublime还是webstorm），打开终端，进入到你的docsify仓库的根目录，然后。。

   ```bash
   git add .
   git commit -m 'first push'
   git push
   ```

   *这样你的docsify项目的代码就同步到了你的github云仓库中*

2. 然后登录你的github账号，进入到你的那个有docsify代码的仓库中。点击Settings

   ![](https://s2.loli.net/2022/03/26/hj75pkElQ83vtXd.png)

   然后转到GitHub Pages那一项，将Source的root目录改成docs目录

   ![](https://s2.loli.net/2022/03/26/hXus149drC58pSM.png)

   点击save之后就可以看到`Your site is published at http://yourname.github.io`，然后按照这个网址进行访问，就是你搭好的docsify文档网站。

# Links

我的这篇教程有少部分是借鉴了[Throwable的教程](https://www.cnblogs.com/throwable/p/13605289.html)，在此将他的链接放出，大家要是想看那篇教程可以直接点进去（其实确实写的挺不错的）。
