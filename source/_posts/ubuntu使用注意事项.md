---
title: Ubuntu使用注意事项
date: 2021-03-10 16:57:22
updated: 2021-08-15 11:17:00
index_img: https://s2.loli.net/2022/03/26/L2DGcHowSeKM9qU.jpg
tags:
- Linux
categories:
- Linux
---


# ubuntu使用的所有注意事项

## 软件相关

### 删除软件

> 我们都知道使用`apt remove apt-name`可以很快地删除软件包，
但是有时候软件还是会有残留。

删除所有的软件数据残留:

```shell
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P
```

## 使用Deepin-Wine安装TIM

> 全是血泪史，这里的Deepin-Wine是深度对Wine的封装，想要知道更多的Wine知识，可以查看[这里](https://www.winehq.org/)

### 安装deepin-wine

> 这里的这种方法适合于有一定基础的Linux用户，你可以自己安装Deepin-Wine环境，然后下载相应的Windows软件。(*较为适用*)

首先你得进入deepin-wine这个项目的[地址仓库](https://github.com/wszqkzqk/deepin-wine-ubuntu)

然后按着文档进行仓库的克隆（*如果太慢的话就用[gitee的库](https://gitee.com/wszqkzqk/deepin-wine-for-ubuntu.git)*）

安装完成之后记得在终端中运行（授予可执行权限后）：`./install.sh`

### 安装TIM

> 如果按着仓库地址上的说明，去这个地址：https://mirrors.aliyun.com/deepin/pool/non-free/d/ 那么你就会发现这个镜像中并没有你想要的linux版本QQ之类的。

通过寻找我们可以发现这个镜像链接已经搬到了 https://packages.deepin.com/deepin/pool/non-free/d/


**接下来就是通过我们下载来的deb包进行安装，完成后就能使用TIM了**

### 解决图像和下载文件问题

> 如果你认为这就结束了，就大错特措了。

linux上的TIM因为支持了IPv6，所以下载安装好的TIM并不能下载文件，所以我们还要更改一下网络设置，禁用IPv6（*具体可参考[这篇文章](https://zhuanlan.zhihu.com/p/161392558)*）

在`/etc/sysctl.conf`中进行编辑,添加以下内容：

```text
# IPv6 disabled
net.ipv6.conf.all.disable_ipv6 =1
net.ipv6.conf.default.disable_ipv6 =1
net.ipv6.conf.lo.disable_ipv6 =1
```

### 开机设置IPv6

经过本人的测试，在一些情况下还是会有关闭了IPv6但是重启TIM之后还是无法下载文件的现象。于是我打算进行一个开机执行命令的操作，在每次开机之后就禁用IPv6。

1. 在/etc/init.d/ 中创建自己的开机运行脚本

```bash
cd /etc/init.d
sudo vim /etc/init.d/mystart.sh
```

2. 然后写入你想要开机执行的命令：

```shell
#!/bin/bash
### BEGIN INIT INFO
# Provides:          RO
# Required-Start:    $local_fs $network
# Required-Stop:     $local_fs
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: self define auto start
# Description:       self define auto start
### END INIT INFO
# 上面的部分也必须写上，后面放上你需要开机执行的命令
sudo sysctl -p
sudo rm -rf ~/.deepinwine/Deepin-TIM
```
3. 修改脚本文件权限

```bash
sudo chmod 755 /etc/init.d/mystart.sh
```
4. 加入开机启动

```bash
sudo update-rc.d mystart.sh defaults 90
```

<u>你可以把这个教程直接当作制作自定义开机脚本的教程</u>

### TIM文件的存贮

> 这下好了，我们可以下载文件了。心满意足之后你会想：我的文件到底下到哪里了？

这里直接告诉你文件路径

```bash
cd /home/你的用户名/.deepinwine/Deepin-TIM/drive_c/users/你的用户名/My Documents/Tencent Files/你的账号/FileRecv
```

可参考[这篇文章](https://blog.csdn.net/qq_33831360/article/details/90611943)

## 使用封装好的Deepin-Wine软件

> 这里其实不是封装好的，其实是作者将所有的Deepin-Wine上的软件做了一个差量仓库，我们可以直接使用这个仓库(*加到我们的apt中*)，具体情况直接看这个[仓库](https://github.com/zq1997/deepin-wine)

<font color=#34b>这种方式适合不想折腾的用户</font>

但是效果不太理想。

## 直接使用Wine

> 既然是Deepin对Wine的封装，那么我们完全有理由直接用Wine，就在我之前说的[官网](https://winehq.org)

<font color=#34b>这种方式适合想折腾的玩家</font>

## ubuntu应用图标的设置

> 你可能遇到这样的境遇，当我想要安装一个idea的时候，我发现安装好之后App列表竟然没有IDEA出现，咋办？

咱们其实可以在`/usr/share/applications`目录下看到系统里所有的应用图标（*没错，你甚至可以更改系统图标的样式和属性*）

然后我们可以新建一个文件来配置咱们IDEA的图标：

```bash
sudo vim intellij-idea.desktop
```

*然后加上相应的配置：*

```text
[Desktop Entry]
Name=IntelliJ IDEA
Comment=IntelliJ IDEA
Exec=/opt/idea-IC-203.7148.57/bin/idea.sh
Icon=/opt/idea-IC-203.7148.57/bin/idea.png
Terminal=false
Type=Application
Categories=Application;Development;IDE;
StartupWMClass=jetbrains-idea-ce
```

<font color=brown>当然，你必须要将IDEA正确的开启路径配置正确。</font>

## 更改通知栏样式

[链接](https://ubuntuqa.com/article/787.html)

## 使用PPA

> 首先你得知道什么是ppa

ppa -> Personal Package Archive, 表示个人软件包存档

我们知道在Debian Linux中安装软件有多种方式：

- 通过直接下载软件包（`.deb`文件）

- 通过自己的(Debian官方提供的)软件库来下载安装软件

- 编译源码，硬核安装（_大佬专属[bing bu shi]_）

<font color="brown">当然我们要想不用源码安装，但是apt的官方仓库中也没有我们想要的软件怎么办？</font>

这个时候我们就需要用到`ppa`了，__此时我们只需要将这个软件的ppa地址添加到我们的apt库，就能使用原有的apt指令来安装这个软件了（就像往常一样）__

_所以，ppa就像是一个第三方的软件库，我们需要手动添加`ppa`来使我们的默认软件库更加丰富庞大（即按需添加）。_

### Example

现在我们来示范一下使用ppa的方式来安装`Ubuntu cleaner`(个人也推荐安装，对新手来说这个清理工具很友好)

```shell
sudo add-apt-repository ppa:gerardpuig/ppa
sudo apt-get update
sudo apt install ubuntu-cleaner
```

_对，就这么简单~_

但是我们的`ppa`被存储到哪里了呢？在这个文件夹下`/ect/apt/sources.list`

## 下载油管视频

这就不得不说`youtube-dl`了，[安装教程](https://github.com/ytdl-org/youtube-dl#installation)

然后这里有一篇 [讲解](https://cc2.cc/youtube-dl.html)

## 使用scrcpy控制安卓

> 说控制这个词有点太过了，但是就是这么酷。你可以使用这个来对你的安卓手机进行PC端的显示和控制(*是电脑控制手机，yeah awesome!*)

**首先咱们看看[地址](https://github.com/Genymobile/scrcpy)**

首先你的手机币需要开启开发者模式中的`USB调试`：一般来说需要在手机的设置中<u>点击手机版本面板7次</u>，拿我的小米手机(<del>雷总打钱</del>)为例:

![多次点击MIUI版本](https://s2.loli.net/2022/03/26/KAQFLpbXm8fRT6W.jpg)

然后就是开启开发者选项的`USB调试`了:

![直接确定吧](https://s2.loli.net/2022/03/26/ng2jV8SvGtpBU17.jpg)

***注意：如果你和我一样是小米用户，那么还要开启`USB调试`下方的`USB调试(安全设置)`!***

然后就可以畅快玩耍了XD！

*ps: 配合termux是真的香*

> 不得不说，这个安卓模拟器真的可以，真的可以直接在Ubuntu上玩手机游戏了(*唯一的缺点是不能传递音频video-only, 但是真正的geek不需要音频*)

### 快捷键

| 操作                              | 快捷键              |
|-----------------------------------|---------------------|
| 全屏                              | MOD+f               |
| 向左旋转屏幕                      | MOD+← (左箭头)      |
| 向右旋转屏幕                      | MOD+→ (右箭头)      |
| 将窗口大小重置为1:1 (匹配像素)    | MOD+g               |
| 将窗口大小重置为消除黑边          | MOD+w 双击¹         |
| 点按 主屏幕                       | MOD+h 鼠标中键      |
| 点按 返回                         | MOD+b 鼠标右键²     |
| 点按 切换应用                     | MOD+s               |
| 点按 菜单 (解锁屏幕)              | MOD+m               |
| 点按 音量+                        | MOD+↑ (上箭头)      |
| 点按 音量-                        | MOD+↓ (下箭头)      |
| 点按 电源                         | MOD+p               |
| 打开屏幕                          | 鼠标右键²           |
| 关闭设备屏幕 (但继续在电脑上显示) | MOD+o               |
| 打开设备屏幕                      | MOD+Shift+o         |
| 旋转设备屏幕                      | MOD+r               |
| 展开通知面板                      | MOD+n               |
| 收起通知面板                      | MOD+Shift+n         |
| 复制到剪贴板³                     | MOD+c               |
| 剪切到剪贴板³                     | MOD+x               |
| 同步剪贴板并粘贴³                 | MOD+v               |
| 注入电脑剪贴板文本                | MOD+Shift+v         |
| 打开/关闭FPS显示 (在 stdout)      | MOD+i               |
| 捏拉缩放                          | Ctrl+按住并移动鼠标 |

要想获得更多的玩法或者想要传递音频可以参考 [文档](https://github.com/Genymobile/scrcpy/blob/master/README.zh-Hans.md)
