---
title: linux课程笔记
date: 2021-03-22 19:34:37
tags:
---

# linux课程笔记

# linux访问

linux使用`VFS`来访问其他格式文件

## linux文件系统

- bin

二进制文件，用户命令（一般常用命令）

- sbin

存放系统命令

- usr

这下面也有bin和sbin，和用户有关的

- cdrom

光驱，光盘

- dev

用设备进行管理，所有的硬件信息

*tty是终端*，还有很多硬盘的信息也是在这里

- etc

所有的配置文件

- lib

库文件

- lost+found

出错信息

- media

早期是在`mnt`上挂在设备，现在是在`media`，如硬盘，u盘的挂载

- proc

存放所有的设备的动态信息

- root

超级用户的主目录

- usr

第三方软件

- var

可变的文件，比如日志文件

## boot文件目录

里面有`efi`，`grub`，**其中efi中是引导程序，grub是相应的grub程序**

**System.map，vmlinuz，initrd.img**这三个文件是和内核有关（当然还要加上那个版本号），其中initrd.img就是根文件系统的印像（建立了虚拟的根目录文件系统，为了引入真正的文件系统）

initrd.img主要是编译的模块

编译内核时的配置文件就是`config-版本-generic`

## 编译内核

UEFI -> grub.cfg -> vmlinuz -> initrd.img

需要编译的文件：vmlinuz -> system.map(映射文件) -> initrd.img -> modules(很多硬件驱动在这里)

模块(modules)的位置: `/lib/modules`

最后编译好了之后写`grub.cfg`(也可以直接`update-grub`)

- 内核功能选项配置：

[*] 加入到内核功能中
[ ] 不加入到内核中
[M] 加入到模块中

`-jN`中的N是选择使用多少个核编译进行编译

### grub目录

最开始的时候是从`sda*`文件中的boot程序，而grub.cfg可以设置默认启动项和内核参数，也就是说引导程序最先是在`sda*`上找程序，然后根据grub.cfg中的配置来进行设置

### 更改grub程序

可以跑到`/etc/default/grub`这个文件中进行配置，可以看到如下文件内容(*只记录非注释信息*)

```text
GRUB_DEFAULT=0
GRUB_TIMEOUT_STYLE=hidden
GRUB_TIMEOUT=10
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
GRUB_CMDLINE_LINUX=""
```

*同时我们还可以到`/etc/grub.d/`这个文件目录下设置grub配置*

其中`10_linux`是有写入到grub.cfg的脚本(*其实每个文件都是一个脚本，之后会全部写到grub.cfg*)

## 查看磁盘情况

- `df -h`

可以查看linux文件系统中的磁盘情况

- `fdisk -l`

可以查看所有的磁盘使用情况(*整台电脑*)

## 软件源地址

软件源 /etc/apt/sources.list

## linux运行环境

可使用tty4来进入真正的终端

使用<kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>F4</kbd>

使用`env`或者`set`得到环境变量（*`set`信息更多*)

## 使用后台方式执行命令

只需要在要执行的命令之后跟上一个`&`就可以后台运行了

## 浏览文件

### `cat`和`more`以及`less`

个人感觉`less`的体验感最好，因为`less`的手感用起来像是vim。
`less`可以随意地上下移动行，甚至使用`/`来进行关键字搜索。

*当然我们还有`head`和`tail`*(可以使用`-n`参数来指定浏览行数)

## 创建文件夹

```bash
mkdir -p dir1/dir2
```

这样就可以在`dir1`这个文件夹不存在的情况下同时生成`dir1`和`dir2`两个文件目录

### `cp`的更多用法

当我们使用`cp`来进行一个文件或者是一个目录的复制的时候，很可能我们复制过来的文件的属性发生了变化（*比如用户组权限，创建时间等*），于是我么可以用`cp -a`来确保我们复制过来的文件所有的信息都不会改变。

*`-r`就可以拷贝目录了。*

如果我们需要在执行`cp`操作时给予一定的响应信息的话，我们可以加上`-i`参数。(*即交互的意思Interactive*)

## `ln`命令

使用`ln -S`产生软链接；使用`ln`产生硬链接

## 打包命令

使用`tar`命令：

`tar -xvzf`解压缩并解包gz格式压缩包

`tar -cvzf`打包并压缩为gz格式压缩包


| 格式 | 命令参数 |
|:-----|:---------|
| gz   | z        |
| xz   | a        |
| bz   | j        |


# 系统信息类命令

- `dmesg`

- `df`

- `du`

- `free`

可查看内存空间情况

cache: 存在于cpu中，用于cpu计算空间的快速存储

buffer: 和外设打交道

- `date`

- ``

## 进程管理命令

`ps aux`: 查看所有进程情况

`1`号进程是所有进程的父进程，`2`号进程是所有类进程的父进程

`S`是等待态，`D`是不可打断的等待态，`Z`是僵尸态

父进程没有回收子进程的资源，于是导致子进程变成了僵尸态（杀都杀不死）

使用`ps -ef`或者使用`pstree`(更有结构)来显示进程的父子关系。

### `kill`命令

使用`kill -l`查看可发送的所有信号。

使用<C-c>是发送的第二个信号（INT），使用<C-z>是发送的第十九个信号（STOP）

### `top`命令

可以动态地显示进程

### 其他杂命令

使用`bg`命令将任务放在后台运行。

使用`fg`命令将任务放到前台运行。

`uname`命令展示基本的系统信息。

`uname -r`查看内核信息

## 文件系统和磁盘管理

使用`chmod`来更改权限

```bash
chmod 777 test
```

## 用户的基本信息

在`/etc/passwd`文件中会有所有用户的信息

在`/etc/shadow`文件中存储着所有用户的密码

## 切换到超级用户

最好使用`su -`来切换到root用户，相比于`su`这样可以将环境变量也切换过来。

## 使用`ifconfig`

一般还是要安装一下`net-tools`，然后输入`ifconfig`就可以查看所有的网卡信息。

当然在windows下，输入`ipconfig`也有一样的功能。



## GCC开发

> 首先GCC和gcc是不一样的，GCC -> GNU Compiler Collection， 而gcc是GCC里面的一个c语言的编译器。

### gcc编译过程

- 预处理
- 编译成汇编代码
- 汇编成目标代码
- 链接

**使用`-l`来链接其他的库**

eg:

```bash
gcc -o test test.c -ltest
```

## 使用gdb来调试程序

通过直接使用`gdb file`来对编译得到的可执行文件进行调试。

### `gdb`所用到的命令

如果是输入`gdb`的话会进入到`gdb`的命令行界面。（*输入`quit`进行退出*）

`gcc -g`来编译的时候可以使得可执行文件能够之后反编译出来。


## 网络通信

很多时候我们都以为是主机之间的通信，其实是进程之间的通信。

作业：使用P2P来完成一有图形界面的通信模式。

- 应用层：内容上的统一协议（语言，格式）
- 表示层：编码，解释
- 会话层：通过中间人来告知通信（断点问题，建立同步点）
- 传输层：将信息暂存到一个传达室，以便将信息传输
- 网络层：查找下一条的通信（但是不知道怎么去下一条），利用ip地址
- 链路层：使用网卡，找到真正的目的主机（使用Mac地址），以便真正的传输。
- 物理层：真正的数据传输，电信号的传输（到光纤为止）

### TCP协议

建立一个可靠的数据传输模式

*TCP协议和UDP协议只在端系统中存在*

### UDP协议

不需要在通信之间建立可靠连接

一对多，多对多

进程标识方法

（协议，本地地址，本地端口号）

### TCP协议发送

#### 客户端socket(CS)

socket() -> connect(CS) -> send(CS) -> receive(CS) -> close(CS)

#### 服务器socket(SS)

socket() -> bind(SS) -> listen(SS) -> ms = accept(SS) -> receive(ms) -> send(ms) -> close(ms)

### 通信的两套系统

BSD 和 Posix

BSD使用AF（地址族），Posix使用PF（协议族）

### UDP协议发送

#### 客户端socket(CS)

CS = socket() -> sentto(CS, SS, buf) -> receivefrom(...) -> close(CS)

#### 服务器socket(SS)

SS = socket() -> bind(SS) -> sentto(CS, SS, buf) -> receivefrom(...)


## 插入模块

`lsmod`: 列出所有的模块

`insmod moduleName`: 插入新模块

`rmmod moduleName`: 移除一个模块
