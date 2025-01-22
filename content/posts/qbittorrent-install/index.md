---
title: "Debian/Ubuntu VPS 安装最新版 qbittorrent"
summary: "使用 qbittorrent-nox-static 二进制文件安装"
date: 2021-02-15 19:23:41
tags: ["qbittorrent", "BT", "VPS"]
categories: ["教程"]
---

有不少人用 aria2 下载 BT，但是相对来说 QBittorrent 还是更好用一点，但是网上没什么一键脚本，还得靠自己动手安装，默认的 apt 源里的 qb 很旧了，还是用新的比较好

安装的版本是 nox 版本，不需要安装任何图形化界面，直接打开网页就可以用。

创建默认配置目录

```bash
mkdir -p ~/.config/qBittorrent
```

创建配置文件

```bash
touch ~/.config/qBittorrent/qBittorrent.conf
```

编辑文件

```bash
nano ~/.config/qBittorrent/qBittorrent.conf
```

添加下面的内容，Web UI 端口可以自行定义

```bash
[LegalNotice]
Accepted=true

[Preferences]
WebUI\Port=8080
General\Locale=zh
```

输入完后，按下`Ctrl+X`，然后输入 y 保存

之后直接下载别人编译好的二进制文件
https://github.com/userdocs/qbittorrent-nox-static/releases/
我现在命令里写的是现在的最新版本文件，如果有新版本了，请自行替换 https://github.com/userdocs/qbittorrent-nox-static/releases/download/release-4.3.3_v1.2.12/amd64-glibc-qbittorrent-nox 部分即可，使用 glibc 版本即可

```bash
mkdir -p ~/bin && source ~/.profile
wget -qO ~/bin/qbittorrent-nox https://github.com/userdocs/qbittorrent-nox-static/releases/download/release-4.3.3_v1.2.12/amd64-glibc-qbittorrent-nox
chmod 700 ~/bin/qbittorrent-nox
```

输入

```bash
~/bin/qbittorrent-nox
```

启动程序，根据程序提示进入你的 vps `IP:8080` 即可
