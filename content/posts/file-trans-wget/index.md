---
title: "使用wget和caddy等简单快速的转移服务器之间的文件"
date: 2021-02-23 00:01:03
tags: ["服务器", "文件"]
categories: ["命令"]
---

主要命令

```bash
echo "XXX.XX {
    root * /home/xxx
    file_server browse
}" >> /usr/local/caddy/Caddyfile
```

```bash
wget -r -np -nH -R index.html -L -N --no-use-server-timestamps https://XXX.XX
```

可以搭配 cloudflare 加速转移
