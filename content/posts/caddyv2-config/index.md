---
title: "Caddy V2常用命令行语句和Caddyfile配置"
summary: "整理了一些常用的语句和配置，供大家参考"
date: 2021-02-16 19:28:25
tags: ["Caddy", "程序"]
categories: ["命令"]
---

# 常用命令行语句

目前仅一条：

## caddy file-server

```bash
caddy file-server
	[--root <path>]
	[--listen <addr>]
	[--domain <example.com>]
	[--browse]
	[--templates]
	[--access-log]
```

启动一个简单但可用于生产的静态文件服务器。

`--root` 指定根文件路径。默认为当前工作目录。

`--listen` 接受侦听器地址。默认值为：80，除非使用`--domain`，否则默认值为：443。

`--domain` 将只通过该主机名提供文件服务，而 Caddy 将尝试通过 HTTPS 提供文件服务，所以如果它是公共域名，请确保首先正确配置了任何公共 DNS。默认端口将更改为 443。

`--browse` 如果请求的目录没有索引文件，`--browse`将启用目录列表。

`--templates` 将启用模板呈现。

`--access-log` 启用请求/访问日志。

此命令禁用管理 API，使在本地开发计算机上运行多个实例更容易。

# Caddyfile

Caddyfile 是一种方便人类使用的 Caddy 配置格式。它是大多数人最喜欢的使用 Caddy 的方式，因为它易于编写、易于理解，并且对于大多数用例来说足够表达。

看起来是这样的：

```
example.com

root * /var/www/wordpress
php_fastcgi unix//run/php/php-version-fpm.sock
file_server
```
