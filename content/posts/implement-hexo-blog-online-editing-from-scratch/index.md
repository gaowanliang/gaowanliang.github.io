---
title: "[已过时] 从零开始实现hexo博客的在线编辑"
summary: "本教程需要服务器（VPS），Serverless版本正在探索，请大家耐心等待"
date: 2019-11-19 09:18:16
tags: ["博客"]
categories: ["教程"]
---

⚠ 本教程需要服务器（VPS），Serverless 版本正在探索，请大家耐心等待

# 前言

我今年五月参加的微信小程序开发赛获得了全国三等奖，微信赠了我 1k 的优惠券
![](https://i.loli.net/2019/11/19/4RsvXH2zO8mJn5w.png)
![](https://i.loli.net/2019/11/19/gW7O9TXAD1PMZiY.png)
不过这个优惠券只能购买没有优惠过的服务器，所以 1k 优惠券也是了了，最后在我的精打细算下，买了一年零三个月的服务器。买来不能闲置着不用啊，于是我准备构建一个在线写 hexo 的平台，这样就可以摆脱平台的束缚，在任意环境下都可以进行写作了。由于这个服务器是新购置的，所以我从零开始写一下相关的内容。

# 配置相关环境

## ~~Nignx~~(Nginx 比较麻烦，下面更新 caddy 的)

配置 Nignx 是关键，虽然很俗，但是这个活不能不干。其实也可以有很多选择，比如 Caddy，~~Apache~~（这年头个人搭建网站还有人用 Apache 吗，配置巨麻烦）

Debian apt 自带的 Nginx 太老，可以通过更新源的方式来解决

```bash
echo deb http://nginx.org/packages/debian/ stretch nginx | sudo tee /etc/apt/sources.list.d/nginx.list
wget http://nginx.org/keys/nginx_signing.key && sudo apt-key add nginx_signing.key
sudo apt update && apt install nginx -y
```

到这一部分就完成了最新稳定版的 Nginx 的安装。需要注意的是，这一步安装的 Nginx 和系统自带的 Nginx 的配置目录略有区别，可以用一下几个简单的命令修正：

```bash
sudo mkdir /etc/nginx/{sites-available,sites-enabled}
sudo mv /etc/nginx/conf.d/* /etc/nginx/sites-available
sudo rmdir -f /etc/nginx/conf.d/
sudo perl -pi -e 's/conf.d/sites-enabled/g' /etc/nginx/nginx.conf
```

当然这个没必要，我就没弄

最后出现这个就是成功了
![](https://i.loli.net/2019/11/19/fWvASTeK84U6xLa.png)

## Caddy

`Caddy`是一个极简的`HTTP`服务器，支持`HTTP/2`静态网页服务器，安装也很快，在很多方面都可以不用安装时间很长的`lnmp`，`lamp`环境了，配置文件也很简单，同时还有一些有趣的扩展，可以非常方便的搭建一个`HTTP`服务器。

可以直接用下面的脚本进行安装，国内主机安装较慢，耐心等一下就好了

**系统要求：**`CentOS 6+`/`Debian 6+`/`Ubuntu 14.04 +`。

本脚本只是一个一键安装+运行控制的脚本，没有其他管理虚拟主机等功能。

执行下面的代码安装`Caddy`，如果想要安装其他扩展可以把名字加到命令后面（`bash caddy_install.sh install xxx,xxx,xxx，`扩展列表[点击查看](https://caddyserver.com/download)）。

```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
#备用地址
wget -N --no-check-certificate https://gwliang.com/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
```

### 使用说明

```
启动：/etc/init.d/caddy start
停止：/etc/init.d/caddy stop
重启：/etc/init.d/caddy restart
查看状态：/etc/init.d/caddy status
查看Caddy启动日志：tail -f /tmp/caddy.log
安装目录：/usr/local/caddy
Caddy配置文件位置：/usr/local/caddy/Caddyfile
Caddy自动申请SSL证书位置：/.caddy/acme/acme-v01.api.letsencrypt.org/sites/xxx.xxx(域名)/
```

等会会教大家如何配置

## NodeJS

NodeJS 是最重要的，没有 Nginx 也得有 NodeJS，这是我们写博客的关键，Debian 自带的 NodeJS 还是比较老的，推荐用 [nvm](https://github.com/nvm-sh/nvm) 安装

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | bash
```

然后**关闭再打开终端**，就可以用了

我目前 NodeJS 最新版是 12.13.0，输入

```bash
nvm install 12.13.0
```

就安装了 NodeJS 12.13.0 版本

![](https://i.loli.net/2019/11/19/CN5elIwdTfQYVmZ.png)

## git

git 直接用 apt 安装就行了，这个不再赘述。

```bash
apt install git
```

# 工作区配置

## 把你的博客源文件上传 Github

为了多端同步，也为了防止服务器不好上传你的文件，这个时候就需要将你的博客的源文件文件上传到 GitHub，由于 Hexo 会吃掉你博客的 master 分支，这个时候就需要先创建一个分支。在你博客源文件的文件夹下打开 Git bash，输入下面的命令

```bash
git checkout -b inside
```

这样就创建了一个 inside 分支，因为我这个博客的主题就是 inside。然后`git push`到 GitHub 上，本地这边的工作就完成了。

## 在服务器上把你的源代码 clone 下来

由于服务器是全新的，我就按全新的来说，首先输入下面命令：

```bash
git config --global user.name "你GitHub的用户名"

git config --global user.email "你GitHub注册时用的邮箱"

ssh-keygen -t rsa -C "你GitHub注册时用的邮箱"
```

默认回车即可，然后输入`cat /root/.ssh/id_rsa.pub`，然后把输出的东西整个复制出来
![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20191127204816.png)
打开[github](https://github.com)，在头像下面点击`settings`，再点击`SSH and GPG keys`，新建一个 SSH，Title 随便。

把刚才输出的一串复制到 key 里即可

输入`ssh -T git@github.com`，问 Are you sure you want to continue connecting (yes/no)?时输入 yes，如果出现你的用户名，那就成功了。

然后在服务器上 clone 一下你新建的分支，通过`-b`选择分支，例如

```bash
git clone -b inside https://github.com/gaowanliang/gaowanliang.github.io.git
```

(要按照自己的实际情况来)

## 安装 Code-server

众所周知，Visual Studio Code 是基于 Electron 的，而 Electron 又是一个“双头怪”——基于 Web 技术的桌面应用平台。但在最近，Coder 的开发者最近做了有趣的尝试，让我们可以把整个 VSCode 放到浏览器中去运行。而 VSCode 写 markdown 的体验也是很舒服的，为了不用自己写一个网页来搞一些复杂的配置，直接用浏览器版的 Vscode 就很舒服，而且还能应急改其他代码，那我们就简单配置一下。

首先在下列网址下载最新版 code-server

> https://github.com/cdr/code-server/releases

下载好后上传到你的服务器，或者直接用 wget 下载。之后 cd 到下载目录，执行下面的代码

```bash
tar -C /opt -xzvf code-server-xxx.tar.gz  // 根据具体的名字来解压
cd /opt
mv code-server*4 code-server
cd code-server
./code-server
```

然后就会出现这样的场景，证明你的服务器可以用，但是不能保证正确运行，还得测试一下

![](https://s1.ax1x.com/2020/03/20/8ciU3Q.png)

按下 Ctrl+C 停止服务，接着执行下面的命令

```bash
./code-server --host 0.0.0.0 --port 9900
```

之后打开你的 IP:9900，如果出现需要你输入密码的界面，说明没啥问题，密码如果你没配置过，会显示在服务器控制台里。(如果是部分服务商，需要在后台开放端口)

![](https://s1.ax1x.com/2020/03/20/8cEi8O.png)

输入密码，进入界面，第一次进入要等很久

![](https://s1.ax1x.com/2020/03/20/8cEbdI.png)

和 VSCode 几乎一模一样，有内味了。由于这个 code-server 是完全独立于系统的，所以你需要重新安装一下 nodejs。当然在安装之前，要先安装一下中文汉化包。由于这个自带的插件商店是残废版，很多插件都没有，需要你自己去下载离线版.vsix 安装，安装之前，需要看一下 VSCode 的版本，点击左上角的三个横杠-Help-About，可以看到 version 显示的版本，我这里是 1.42.0，然后去商店下载离线汉化包，进入 https://marketplace.visualstudio.com/ ，搜索 Chinese，然后会出来 Chinese (Simplified) Language Pack for Visual Studio Code 这个，点进去，找到右边的 Download Extension，点一下，获取一下下载连接，我获取到的下载链接是 https://marketplace.visualstudio.com/_apis/public/gallery/publishers/MS-CEINTL/vsextensions/vscode-language-pack-zh-hans/1.43.9/vspackage 可以看到最新版本已经是 1.43.9 了，我们把下载链接里的那个版本号改成适用于你 VSCode 的版本号，比如我就是 1.42.0，所以下载链接就改成 https://marketplace.visualstudio.com/_apis/public/gallery/publishers/MS-CEINTL/vsextensions/vscode-language-pack-zh-hans/1.42.0/vspackage 。下载好后，传到服务器里

回到网页，打开插件列表，选 Install VSIX

![](https://s1.ax1x.com/2020/03/20/8cmW0H.png)

找到上传的地址，安装即可
之后点一下右下角的 YES 即可，之后等待刷新完成，就是中文了
![8cKAYR.png](https://s1.ax1x.com/2020/03/20/8cKAYR.png)

按下 Ctrl+J，打开终端，第一次打开要选一下默认终端的类型，按照图中的方法操作，最后在弹出的框中选择 Bash 即可。

![8cKHc6.png](https://s1.ax1x.com/2020/03/20/8cKHc6.png)

之后按照上面的操作，在这个终端里安装一下 nodejs 和导入一下 GitHub 的信息，只不过不用再生成 SSH 密钥了（`ssh-keygen`和`cat`这两个命令不用了，也不用导入密钥了），但是 git config 命令要改成这样

```bash
git config --system user.name "你GitHub的用户名"

git config --system user.email "你GitHub注册时用的邮箱"
```

之后选择左上角三个横杠-文件-打开文件夹，打开你 clone 的博客的目录

之后要在终端里 npm install，由于我是国内服务器，就用 cnpm install 了（要使用 cnpm 的话，要执行一下`npm install -g cnpm`）

![](https://s1.ax1x.com/2020/03/20/8c3ndU.png)

之后执行一下`npm install -g hexo-cli`，就可以愉快的使用`hexo g && hexo d` 了

## 配置 systemctl

先在服务器终端按下 Ctrl+C 停止服务，然后执行下面的命令

```bash
cat > /etc/systemd/system/code.service <<EOF
[Unit]
Description=Code Server NO.1
After=network.target

[Service]
Type=simple
EnvironmentFile=/opt/code-server/.envfile
WorkingDirectory=/opt/code-server/
Restart=on-failure
RestartSec=10

ExecStart=/opt/code-server/code-server --host 0.0.0.0 --port 9900
StandardOutput=file:/opt/code-server/output/output.log
StandardError=file:/opt/code-server/error/error.log

[Install]
WantedBy=multi-user.target
EOF
```

之后要配置一下默认密码，也是执行下面的命令（将 gaowanliang 改成你自己的密码）

```
PASSWORD=gaowanliang
echo "PASSWORD=\"${PASSWORD}\"" > /opt/code-server/.envfile
```

之后执行下面两条命令，实现后台运行和开机自启

```
systemctl enable code
systemctl start code
```

现在可以直接打开你的 IP:9900，输入你自己设置的密码，这样关闭服务器终端也不会停止了

## 配置域名(可选)

如果你有自己的域名，可以绑上一个域名，就不用每次都输 IP 和端口了，执行下面的命令（将所有 code.gwliang.com 改成你自己的域名）
这里使用的是`Caddy`

```bash
// 下面两个二者选其一
// 不需要HTTPS
echo "http://code.gwliang.com {
  gzip
  proxy / 127.0.0.1:9900
}" > /usr/local/caddy/Caddyfile

//需要HTTPS，其中 admin@gwliang.com 改成你自己的邮箱
echo "code.gwliang.com {
  gzip
  tls admin@gwliang.com
  proxy / 127.0.0.1:9900
}" > /usr/local/caddy/Caddyfile

/etc/init.d/caddy start
```

打开你自己的域名，就可以正常使用了。

# 后记

图片的话不太好上传，直接用在线图床即可，推荐两个

> 路过图床 https://imgchr.com/

> sm.ms https://sm.ms/

用 qq 自带的截图，截好之后直接 Ctrl+V 可以直接上传到网页上，然后下面都会给出 Markdown 的引用方式，直接引用即可
