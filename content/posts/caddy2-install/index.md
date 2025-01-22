---
title: "Caddy 2 快速简单安装配置教程"
summary: "Caddy 2 是一个简单易用的 Web 服务器，支持 HTTP/2、自动 HTTPS、虚拟主机、反向代理、静态文件服务、目录列表等功能。"
date: 2020-11-10 17:53:10
tags: ["建站", "Caddy"]
categories: ["教程"]
---

# 下载安装

目前有三种安装途径，分别为官网(下面)、Github、各系统包管理。

官网的最方便，因为下载后直接就是二进制单文件，不需要多余的操作步骤（而且还能附加插件），而 Github 的是压缩包还需要解压、包管理则还需要修改包管理源。

```bash
# 创建文件夹并进入（自己可以改）
mkdir /usr/local/caddy && cd /usr/local/caddy

# 下载 Caddy 文件，Caddy 2 不再支持 32 位系统了，如果要安装某些插件，还要进 https://caddyserver.com/download 去挑选一下
wget -N --no-check-certificate -O "caddy" "https://caddyserver.com/api/download?os=linux&arch=amd64"

# 赋予执行权限
chmod +x caddy
```

# 配置示例

Caddy 2 可以用命令行启动 HTTP 服务，也可以用配置文件（默认：Caddyfile，当然新版也实现了用 json 这种方式控制，对于设计自动化和程序化的服务更简单了，但是用 caddyfile 习惯了，纯写还是 caddyfile 简单有效）。

不过命令行方式启动只适合于临时使用，毕竟默认是前台运行，所以还是用配置文件吧。
下列示例中，XXX.XX 为你的域名，如果要用 IP，请改成 :8080 ！

## 静态文件服务：

```bash
# 下面的几行命令是一个整体，请修改后一起复制粘贴去运行
# /usr/local/caddy/Caddyfile 为默认配置文件位置，可以改
# /home/xxx 为网站目录路径，如果省略则为 caddy 程序所在文件夹

echo "XXX.XX {
    root * /home/xxx
    file_server
}" >> /usr/local/caddy/Caddyfile
```

## 目录列表服务：

```bash
# 下面的几行命令是一个整体，请修改后一起复制粘贴去运行
# /usr/local/caddy/Caddyfile 为默认配置文件位置，可以改
# /home/xxx 为网站目录路径，如果省略则为 caddy 程序所在文件夹

echo "XXX.XX {
    root * /home/xxx
    file_server browse
}" >> /usr/local/caddy/Caddyfile
```

## 反向代理服务：

```bash
# 下面的几行命令是一个整体，请修改后一起复制粘贴去运行
# /usr/local/caddy/Caddyfile 为默认配置文件位置，可以改
# YYY.YY 是目标网站，https:// 和 :8080 省略后分别默认为 https 协议和 443 端口

echo "XXX.XX {
    reverse_proxy https://YYY.YY:8080
}" >> /usr/local/caddy/Caddyfile
```

写好配置后，可以用这个命令检查下配置文件格式是否正确：

```
/usr/local/caddy/caddy validate
```

# 启动停止

Caddy 2 直接集成启动/停止等命令了，连系统服务脚本都不需要了！

当然如果你想要守护进程，那么你还需要根据官网教程配置 systemd 系统服务。

## 启动

```bash
# start 命令是后台运行（可以关闭 SSH 终端），而 run 命令是前台运行。
# 启动成功会提示：Successfully started Caddy (pid=XXXX) - Caddy is running in the background

/usr/local/caddy/caddy start --config /usr/local/caddy/Caddyfile --adapter caddyfile
```

## 停止

```bash
# 停止成功会提示：stopping now, bye!!

/usr/local/caddy/caddy stop
```

## 重载配置

```bash
# 修改配置文件后不需要重启 Caddy ，重载一下就行了！

/usr/local/caddy/caddy reload
```

## 其他说明

如果嫌弃每次都要用 `/usr/local/caddy/caddy`执行命令，那可以做个软链接：

```bash
# 软链接类似于 Windows 的快捷方式
ln -s /usr/local/caddy/caddy /usr/bin/caddy
```

这样你就可以把 `/usr/local/caddy/caddy start` 等命令简化为 `caddy start` 了！
