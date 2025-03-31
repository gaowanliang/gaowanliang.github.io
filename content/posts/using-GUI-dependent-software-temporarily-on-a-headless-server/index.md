---
title: 在没有 GUI 的服务器上临时使用必须要 GUI 的软件
date: 2025-03-31
tags:
  - Linux
  - VNC
  - x11
  - x11vnc
  - MCA Selector
categories:
  - 技术
  - 软件
---

服务器上跑了个 MC 服务器，更新了最新的版本，准备把没用的区块清理一下方便生成新群系，但是删除区块需要使用 MCA Selector，这个软件必须要 GUI 环境。地图文件有点大，1 个多 G，下载下来删完了再传上去有点太麻烦了，所以需要想办法直接用这个软件。

有两种办法：

1. 装一个桌面环境，例如 xfce 之类的轻量级桌面
2. 虚拟桌面

好在 Linux 足够自由，因为只是临时用用，装一个桌面环境需要好几个 G 的空间，而且也不常用，所以我选择方案 2，我们使用 x11 进行接下来的操作，安装下面的软件：

```bash
apt install -y xvfb x11vnc
```

这样就装好了虚拟桌面环境，然后使用下面的语句开启桌面：

```bash
nohup Xvfb :1 -screen 0 1920x1080x24 -ac -dpi 96 +extension RANDR -nolock > xvfb.log 2>&1 &
x11vnc -display :1 -forever -bg -rfbport 35999
```

第一句是使用 xvfb 在后台的 `:1` 开启了一个 1080p 的虚拟桌面，第二句就是在 35999 端口开启了 vnc，这个时候我们就可以使用 vnc 连上了。

之后我们就可以运行我们的软件，这里要想办法找个带 JavaFX 的 JDK/JRE，可以去这里下载：[Java 8, 11, 17, 21, 23 Download for Linux, Windows and macOS](https://www.azul.com/downloads/)

```bash
DISPLAY=:1 java -Xmx6G -jar mcaselector-2.5.1.jar
```

`DISPLAY=:1` 表示展示在 1 上，效果如图所示

![image.webp](image.webp)

但是窗口有点小，但是这里也没有可以拖的地方，这个时候我们就需要再开一个 SSH 连，使用 xdotool 使用代码对程序进行控制。首先需要列出所有的窗口内容：

```bash
DISPLAY=:1 xdotool search . getwindowname %@
```

得到以下的信息：

```bash
Defaulting to search window name, class, and classname
java
java-lang-Thread
MCA Selector 2.5.1
```

这个时候我们就需要寻找一下 MCA 的窗口 ID：

```bash
DISPLAY=:1 xdotool search "MCA Selector 2.5.1"
```

得到：

```bash
Defaulting to search window name, class, and classname
14680067
```

这个号码每个人可能不太一样，根据实际情况来。然后需要将窗口移动到左上角，并修改为最大的大小：

```bash
DISPLAY=:1 xdotool windowmove 14680067 0 0
DISPLAY=:1 xdotool windowsize 14680067 1920 1080
```

![image1.webp](image1.webp)

这样就最大化了。

以下内容是抄的，以便不时之需:

```bash

xdotool key [key name]
xdotool key alt+Tab
xdotool type ''
xdotool search --name [window name] key [key name] # 查找窗口并按键
xdotool mousemove x y
xdotool mousemove x y click 1  # 1左键 2滚轮 3右键 4向上滚 5向下滚
xdotool search --title "..."
xdotool key Return  # 模拟回车键
xdotool keydown/keyup super # 按下Win键不放
xdotool key Super_L # 左Win
watch -n 10 xdotool key Return # 搭配watch使用，实现循环10秒敲击一次回车
xdotool search "Firefox"  # 获取窗口名称
xdotool getwindowname 39845889 # 在以上指令列出的ID中获取容器名称
xdotool getactivewindow # 获取当前激活的窗口
xdotool windowminimize # 最小化窗口
xdotool windowminimize $(xdotool getactivewindow) # 最小化当前窗口
xdotool key ctrl+l BackSpace # 点击ctrl+l，然后是BackSpace键
xdotool search --name gdb key ctrl+c # 在窗口名为gdb上点击ctrl+c
xdotool mousemove_relative 10 10 # 鼠标相对移动
xdotool mousemove_relative --sync 10 10 # 异步鼠标相对移动
xdotool click -repeat 1 3  # 鼠标右键点击1次
xdotool mousedown/mouseup
xdotool getmouselocation # 获取鼠标位置
xdotool getmouselocation --shell # 获取鼠标位置(便于获取数据)
1 eval $(xdotool getmouselocation --shell)
2 echo $X,$Y
# 即可获得X，Y位置
xdotool getactivewindow windowmove 10 10 # 移动当前窗口位

xdotool search . getwindowpid %@ # 查找所有窗体，所在的 进程号（进程号一样，说明是同一个进程）

xdotool search . getwindowname %@ # 查找所有窗体，并显示窗体的title
```
