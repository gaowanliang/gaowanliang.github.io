---
title: "[已过时] 免费开源的bitwarden_rs自建密码管理系统-安装,使用和备份教程"
summary: "[已过时] 通过简单的几个步骤，轻松的搭建一个密码管理平台"
date: 2020-05-04 10:06:40
tags: ["密码管理", "bitwarden"]
categories: ["教程"]
---

> [!WARNING]
> 此教程已过时，不再适用，仅供参考

# 前言

原来的时候看过 Testv 介绍 1Password 的视频，可以全平台自动填充密码，自动生成无规律密码，看完之后很心动，毕竟密码安全也很重要，但是 1Password 太贵了，一年至少需要 35 美元。而且 1Password 还只能使用信用卡付款，像 Paypal 也不支持，这让很多想尝试 1Password 的朋友“望而却步”了。这篇文章主要分享一下在功能上和使用体验上媲美 1Password 的免费开源密码管理系统 Bitwarden，自建 Bitwarden，再也不用担心 1Password 删库“跑路”了。

# bitwarden_rs 安装教程

> 详细安装视频：https://www.bilibili.com/video/BV15t4y117UU

> 网站：\
> 官网：https://bitwarden.com/ \
> 第三方项目：https://github.com/dani-garcia/bitwarden_rs

Bitwarden 官方推荐使用 Docker 镜像安装，但是 Bitwarden 服务器使用 .Net 开发，如果使用 Docker 来部署，镜像体积过大；此外它使用 MSSQL 数据库，部署这个数据库对服务器配置要求比较高。

而 bitwarden_rs 是第三方开发的 Bitwarden 安装镜像。bitwarden_rs 采用 Rust 实现了 Bitwarden 服务器，这个实现更进一步降低了对机器配置的要求，并且 Docker 镜像体积很小，部署非常方便。此外，官方服务器中需要付费订阅的一些功能，在这个实现中是免费的。测试在 512MB 内存上机子完美运行 bitwarden_rs。

## 安装前准备

- 一台 VPS 主机。bitwarden_rs 使用 Docker 镜像来安装，需要使用一台 VPS 主机，配置不用太高，用 512MB 内存也能跑起来，而且非常地流畅。
- 一个域名。虽然有市面上有少的免费域名，但是建议还是使用付费的域名，密码这个东西比较重要，一旦确定要自建密码管理系统的话就要保证可以长期使用。

## 安装 Docker

你需要先安装 Docker CE (社区版)和 Docker Compose，以下命令是基于 Debian 系统的演示，如果你是 CentOS、Ubuntu，请参考官网：

> 安装 Docker CE (社区版)：https://docs.docker.com/install/linux/docker-ce/ubuntu/\
> 安装 Docker Compose：https://docs.docker.com/compose/install/#install-compose

如果你已经安装过，就不需要看这一步了

Debian 系统安装 Docker CE 和 Docker Compose 如下：

```bash
#卸载()
sudo apt-get remove docker docker-engine docker.io containerd runc
#SET UP THE REPOSITORY
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
#检测是否安装成功
sudo apt-key fingerprint 0EBFCD88
#有以下输出就表示成功添加Docker’s official GPG key:
root@hostfbc9e58d73:~# sudo apt-key fingerprint 0EBFCD88
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
#添加stable repository
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/debian \
   $(lsb_release -cs) \
   stable"
#开始安装
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
#验证 Docker Engine - Community是否安装正确
sudo docker run hello-world
#有以下输出就表示正确安装
root@hostfbc9e58d73:~# sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:b8ba256769a0ac28dd126d584e0a2011cd2877f3f76e093a7ae560f2a5301c00
Status: Downloaded newer image for hello-world:latest
Hello from Docker!
This message shows that your installation appears to be working correctly.
To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.
To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash
Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/
For more examples and ideas, visit:
 https://docs.docker.com/get-started/


#安装 Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
#检测是否安装成功
docker-compose --version
#有输出即表示成功
docker-compose version 1.25.5, build 4667896b
#如果提示命令不存在，可以手动创建链接
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

## 安装 Caddy V2

bitwarden_rs 官方可以选择使用 Caddy 来搭建 Web 服务，主要原因是因为 Caddy 安装配置简单。

安装方法点击 https://gwliang.com/2020/11/10/caddy2-install/

当然你也可以选择使用 Nginx，下面也会给出 Nginx 的配置，如果你没有用过 HTTP Server，推荐使用 Caddy V2 来上手.

## 安装 Caddy（已失效，仅作存档）

安装方法点击 https://gwliang.com/2020/01/27/caddy-install

## bitwarden_rs 安装

bitwarden_rs 安装只需要一句代码就可以搞定：

```bash
docker pull bitwardenrs/server:latest
```

# bitwarden_rs 配置方法

## 启动 bitwarden_rs

启动 bitwarden 也很简单，我们只需要执行以下代码即可：

```bash
docker run -d --name bitwarden \
  -e SIGNUPS_ALLOWED=true \
  -e WEBSOCKET_ENABLED=true \
  -e LOG_FILE=/data/bitwarden.log \
  -p 8880:80 \
  -p 3012:3012 \
  -v /bw-data/:/data/ \
  bitwardenrs/server:latest
```

## 设置 Caddy V2 / Caddy / Nginx

### Caddy V2

由于 V1 版本停止服务了，所以这里更新一下 Caddy V2 的代码，比 V1 还简单，在`/usr/local/caddy/Caddyfile`里输入下面的内容即可（要改下面的域名）

```
pwd.gwl.wtf {
        reverse_proxy 127.0.0.1:8880
        reverse_proxy /notifications/hub 127.0.0.1:3012
        reverse_proxy /notifications/hub/negotiate 127.0.0.1:8880
}
```

### Caddy（停止服务，代码仅作存档）

如果你使用的是 Caddy，在`/usr/local/caddy/Caddyfile`里输入下面的内容即可（要改下面的域名）

```
pwd.gwl.wtf {
  gzip
  tls asdf@q.com
  proxy / 127.0.0.1:8880 {
   websocket
   header_upstream -Origin
  }
  proxy /notifications/hub 127.0.0.1:3012 {
   websocket
   header_upstream -Origin
  }
  proxy /notifications/hub/negotiate 127.0.0.1:8880 {
   websocket
   header_upstream -Origin
  }
}
```

### Nginx

如果你使用的是 Nginx，可以使用下面的配置（先要配置好了域名、SSL 等）

```
server {
  listen 80;
  listen 443 ssl http2;
  ssl_certificate /usr/local/nginx/conf/ssl/pwd.gwl.wtf.crt;
  ssl_certificate_key /usr/local/nginx/conf/ssl/pwd.gwl.wtf.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  ssl_ciphers TLS13-AES-256-GCM-SHA384:TLS13-CHACHA20-POLY1305-SHA256:TLS13-AES-128-GCM-SHA256:TLS13-AES-128-CCM-8-SHA256:TLS13-AES-128-CCM-SHA256:EECDH+CHACHA20:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
  ssl_prefer_server_ciphers on;
  ssl_session_timeout 10m;
  ssl_session_cache builtin:1000 shared:SSL:10m;
  ssl_buffer_size 1400;
  add_header Strict-Transport-Security max-age=15768000;
  ssl_stapling on;
  ssl_stapling_verify on;
  server_name pwd.gwl.wtf;
  if ($ssl_protocol = "") { return 301 https://$host$request_uri; }

   client_max_body_size 128M;
    location / {
        proxy_set_header  Host  'pwd.gwl.wtf';
        proxy_pass http://127.0.0.1:8880;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
     }
    location /notifications/hub {
    proxy_pass http://127.0.0.1:3012;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
    }
    location /notifications/hub/negotiate {
    proxy_pass http://127.0.0.1:8880;
    }
}
```

重启 Caddy/Nginx，刷新一下浏览器，你可以看到 bitwarden 已经可以正常访问了。

![](https://s1.ax1x.com/2020/05/04/Y9QKpj.png)

## 禁止注册

为了保证 bitwarden 服务器的稳定，你可以禁止 bitwarden 注册新用户（这一项可以放在最后，否则你自己都无法注册了）。禁止新用户注册：

```
docker run -d --name bitwarden \
  -e SIGNUPS_ALLOWED=false \
  -v /bw-data/:/data/ \
  -p 80:80 \
  bitwardenrs/server:latest
```

## 关闭、重启、升级

```
关闭： docker stop bitwarden
启动： docker start bitwarden
```

需要升级的话，你只需要拉取最新的 image，rm 原来的 image，然后按之前的方法启动一个新的容器即可

```
# Pull the latest version
docker pull bitwardenrs/server:latest

# Stop and remove the old container
docker stop bitwarden
docker rm bitwarden

# Start new container with the data mounted
docker run -d --name bitwarden \
  -e SIGNUPS_ALLOWED=true \
  -e WEBSOCKET_ENABLED=true \
  -e LOG_FILE=/data/bitwarden.log \
  -p 8880:80 \
  -p 3012:3012 \
  -v /bw-data/:/data/ \
  bitwardenrs/server:latest
```

# Docker Compose 配置

**这一步可选**。使用 Docker Compose 配置可以方便你管理 Docker，不用也可以。 在 bitwarden 目录创建 docker-compose.yml 文件

```
nano docker-compose.yml
```

在 docker-compose.yml 写入一下配置，参考官方 wiki（https://github.com/dani-garcia/bitwarden_rs/wiki/Using-Docker-Compose）

```bash
version: "3"
services:
  bitwarden:
    image: bitwardenrs/server
    container_name: bitwardenrs
    restart: always
    ports:
        - "127.0.0.1:8880:80" #将8880端口映射到镜像80端口
        - "127.0.0.1:3012:3012"
    volumes:
      - ./bw-data:/data
    environment:
      WEBSOCKET_ENABLED: "true" #开启WebSocket
      SIGNUPS_ALLOWED: "true" #开启注册，自己注册后改成fale
      WEB_VAULT_ENABLED: "true" #web客户端\
```

## 运行服务

```bash
docker-compose up -d #运行服务
docker-compose down #关闭服务
docker-compose restart #重启服务
```

# bitwarden 管理密码

## 网页版 bitwarden

网页版的 bitwarden 管理密码也是非常地方便。以下是 bitwarden 的界面，有中文：

![](https://s1.ax1x.com/2020/05/04/Y9YrzF.png)

这是 bitwarden 的设置页面

![](https://s1.ax1x.com/2020/05/04/Y9YRd1.png)

可以自己设置两步验证，加强安全性。

## 浏览器 bitwarden

bitwarden 提供了 Chrome、Firefox 等浏览器插件，直接安装就可以启用。

在插件设置中填入你自己的 bitwarden 地址（否则是进入到官方的地址）

![](https://s1.ax1x.com/2020/05/04/Y9YLdI.png)
![Y9tSSS.png](https://s1.ax1x.com/2020/05/04/Y9tSSS.png)

更多设置等我出视频即可

# bitwarden 备份与恢复

## 手动备份

bitwarden 备份最关键的就是 sqlite3 数据库文件了，你可以直接进入到`/bw-data`目录下，将 sqlite3 数据库保存下来。当然你也可以将 bw-data 目录下所有的包括附件文件夹、图标缓存文件夹、密钥等文件备份下来。

## 自动备份

利用定时任务执行 bitwarden 备份，首先新建一个文件夹：mkdir /bw-data/db-backup，然后执行数据库备份任务。代码如下（关于定时任务可以自行搜索`Crontab定时任务教程`）：

```
sqlite3 /bw-data/db.sqlite3 ".backup '/bw-data/db-backup/backup.sqlite3'"
```

## 恢复备份

bitwarden 要恢复备份的话也很简单，直接将 sqlite3 数据库重写替换就可以了，其它的几个文件夹可以覆盖，也可以保留新的，不影响。如果你的数据库路径与原来的不一致，可以手动指定，代码示例：

```
docker run -d --name bitwarden \
  -e DATABASE_URL=/database/bitwarden.sqlite3 \
  -v /bw-data/:/data/ \
  -v /bw-database/:/database/ \
  -p 80:80 \
  bitwardenrs/server:latest
```

## 七牛云备份

本地备份只能防止数据出错，有恢复的余地。但是一旦服务器宕机，或者是无法连接，数据就取不出来了，这样就麻烦了。所以我们可以考虑使用七牛云备份到云服务器上，即使服务器宕机也可以把数据轻松下载下来。

点击[这里](https://github.com/qiniu/qshell)查看和下载 qshell。解压并命名为 qshell（应该是没有后缀名的），上传到服务器要执行该命令的用户目录里，比如我是用户名为 aliyun 的用户要执行该命令，就上传到/home/aliyun/upload/qiniu/目录下

### 给 qshell 授权

root 用户下授予 qshell 运行权限，开放给所有人

```
chmod a+x qshell
```

## 配置好 qshell 的 ak 和 sk

ak 和 sk 可以在七牛云“个人中心”-->“密钥管理”查看。（存储空间的完整名称，就是你在对象存储里新建的存储空间名字）

```su aliyun
./qshell account 你的access_key 你的secret_key aliyun（aliyun是我为qshell起的用户名，随便起）
```

完成之后检验一下是否添加成功：

```
./qshell account
```

### 填写 qshell 配置文件

填写好上传配置文件，命名为 upload.conf，上传到 qshell 同一目录里，如果不清楚具体规则可以查看 qshell 的官方 git 主页。

我这里给大家一份我用的备份信息,大家可以根据这个进行修改

```
{
   "src_dir"            :   "/bw-data",
   "bucket"             :   "bitwarden-bak",
   "file_list"          :   "",
   "key_prefix"         :   "",
   "up_host"            :   "",
   "overwrite"          :   true,
   "check_exists"       :   false,
   "check_hash"         :   false,
   "check_size"         :   false,
   "rescan_local"       :   true,
   "skip_file_prefixes" :   "",
   "skip_path_prefixes" :   "",
   "skip_fixed_strings" :   "",
   "skip_suffixes"      :   "",
   "log_file"           :   "/opt/qshell/upload.log",
   "log_level"          :   "info",
   "log_rotate"         :   1,
   "log_stdout"         :   false,
   "file_type"          :   0
}
```

之后执行`./qshell qupload upload.conf`查看上传效果

### 使用 crontab 定时任务进行定时备份

```bash
crontab -e # 进入cron定时界面

* 2 * * * /opt/qshell/qshell qupload /opt/qshell/upload.conf > /dev/null # 其中的路径要填写完整路径
```

# 总结

bitwarden 作为一款开源的密码管理器，其本身支持平台之多，功能之全面，是替代 1Password 的理想品。bitwarden 安装简单，采用 Docker 镜像，特别适合已经有了 VPS 建站的朋友，在不影响建站的情况下正常使用 bitwarden。

bitwarden 可以支持多用户注册使用，如果想要让 bitwarden 发送邮件的话，可以使用 SMTP 发邮件，参考代码如下：

```
docker run -d --name bitwarden \
  -e SMTP_HOST=<smtp.domain.tld> \
  -e SMTP_FROM=<bitwarden@domain.tld> \
  -e SMTP_PORT=587 \
  -e SMTP_SSL=true \
  -e SMTP_USERNAME=<username> \
  -e SMTP_PASSWORD=<password> \
  -v /bw-data/:/data/ \
  -p 80:80 \
  bitwardenrs/server:latest
```

本文部分内容参考自：[wzfou](https://wzfou.com/bitwarden-rs/) [bitwarden_rs Wiki](https://github.com/dani-garcia/bitwarden_rs/wiki/SMTP-configuration)。
