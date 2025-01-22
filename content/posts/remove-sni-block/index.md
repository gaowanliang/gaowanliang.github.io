---
title: "使用简单方法解决SNI阻断问题"

date: 2020-02-21 15:58:33
tags: ["SNI", "HTTPS", "TLS"]
categories: ["技术"]
---

## SNI 阻断技术简单介绍

TLS 是一个伟大的技术，它确保了网络传输的内容不被中间人篡改。现在越来越多的网站正在使用 HTTPS（即 HTTP over TLS）来保护网页内容。与此同时，TLS 设计中的一个缺陷，却使得阻断 TLS 连接变得可控。

在建立新的 TLS 连接时，客户端（如浏览器）发出的第一个握手包（称为 Client Hello）中，包含了想要访问的域名信息（称为 `SNI，Server Name Indication`）。某些服务器（比如 CDN）会同时支持多个域名，在加密传输之前，它需要知道客户端访问的是哪个域名。于是 SNI 必须以明文的方式传输。并且由于浏览器并不知道服务器是否需要 SNI，浏览器会对所有的 TLS 握手都加入 SNI。

于是，大家都懂的。根据黑名单，某些防火墙对于 TLS 连接可以进行精确地阻断。

目前 Mozilla 和 CloudFlare 主导了一项对 SNI 的改进方案，称为 `Encrypted SNI (ESNI)`。这个提案还在早期的讨论状态中，目测还需要两年时间才可以定稿和推广。现阶段只有 Firefox Nightly（客户端），以及 CloudFlare 和 Wikipedia（网站）支持初代的 ESNI。

在 ESNI 正式推广之前，我们还需要其它的技术来避开对于 TLS 连接的探测。

目前我们还没有发现通用的解决方案。有一个较为通用的，但部署起来略麻烦的方案称为 `Domain Fronting` ([域前置](https://zh.wikipedia.org/wiki/%E5%9F%9F%E5%89%8D%E7%BD%AE)，又叫域名幌子)。它的原理简单来说是这样的：部分服务器允许 TLS 连接说自己需要域名 A，但之后的 HTTP 协议说自己需要域名 B。或者服务器压根就不看 SNI 的信息。在这种情况下，对于一个黑名单的域名， 我们在建立 TLS 的时候，可以选用一个不在黑名单的域名，绕过对 TLS 连接的监测。

当然，它的缺点是，依赖于服务器行为。也就是说，每个不同的站点，可能都需要不同的策略（域名）。

举两个例子：

P 站的服务器实际上是不看 SNI 的。在建立 TLS 连接的时候，即使不携带 SNI，也可以正常进行 HTTPS 访问。
zh.wikipedia.org 是一个黑名单域名，但同站点的 www.wikipedia.org 就不是。我们在建立 TLS 连接时，使用 SNI = www.wikipedia.org ，之后的 HTTP 请求依然可以正常连到 zh.wikipedia.org。
顺便说一句，`Domain Fronting` 实际上不是一个合理的用法，网站完全可以**拒绝这类连接**，比如 Google 和 Amazon 就主动在自己的所有服务中拒绝这项技术。即使这样，`Domain Fronting` 依然是在 ESNI 之前最好的绕过技术。

下面我们将使用一个工具来解决 SNI 阻断问题

## 过程

在看此教程之前，请确保你已经安装了`OpenSSL`，具体安装过程不再详述，可以在 CMD/Powershell/bash/Terminal 中输入`openssl`,如果出现`OpenSSL>`字样，说明已经安装

### 创建自签名 CA 证书

使用 CMD/Powershell/bash/Terminal 等命令行工具打开任意文件夹，依次输入下面的命令
（其中的 openssl.cnf 可以在 openssl 的安装处找到，如果没有可以下载[这个](https://cdn.jsdelivr.net/gh/gaowanliang/gitment-store/openssl.cnf)，放在命令行工具打开的文件夹下)

```bash
# 生成根证书私钥(key文件)
openssl genrsa -out ca.key 2048
# 生成根证书签发申请文件(csr文件，申请过程可一路回车)
openssl req -new -key ca.key -out ca.csr -config ./openssl.cnf
# 自签发根证书(crt文件)
openssl x509 -req -days 3650 -in ca.csr -signkey ca.key -out ca.crt
# 生成pem格式证书
cat ca.crt ca.key > ca.pem
```

Windows 下可以直接打开 ca.crt 文件并安装证书，Linux 等系统请自行根据版本进行安装。还可以通过浏览器来进行安装（系统直接安装和浏览器安装二选一），这里以 Chromium 系浏览器为例。

在设置中搜索证书：
![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200221172943.png)

点击管理证书后面的按钮，并找到“受信任的根证书颁发机构”：

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200221173108.png)

导入刚才的 ca.crt 即可，之后 Windows 系统需要重启才能使证书生效，Linux 请根据实际情况进行操作。

### 通过工具解决 SNI 阻断

> 下载地址 https://github.com/bypass-GFW-SNI/main/releases

> 或使用这个地址 https://www.lanzous.com/b0bec39pa 密码:hwmy

根据你的系统进行下载，Linux 系统需要给文件 777 权限

同时下载这个文件 https://www.lanzous.com/i9jthcb

将下载的文件放入刚才生成证书的文件夹，使用命命令行工具执行下面的命令

```bash
# Windows
.\bypass-v1.2-windows-amd64.exe -c .\ca.pem -k .\ca.key -l .\domain.conf
# Linux
.\bypass-v1.2-linux-amd64 -c .\ca.pem -k .\ca.key -l .\domain.conf
```

如果提示`"listen tcp [::1]:443: bind: An attempt was made to access a socket in a way forbidden by its access permissions."`则是 443 端口被占用，请自行搜索解决

### 最后设置

以下两个方法二选一，都可以达到目的

- 将本机的 DNS 设置为 127.0.0.1 设置方法请自行搜索解决
- 设置 hosts 可以下载 [这个](https://cdn.jsdelivr.net/gh/googlehosts/hosts/hosts-files/hosts) 文件，用记事本打开，搜索相关网站（如 pixiv 等），将前面的 IP 改为 127.0.0.1，效果如下：

![image.png](https://i.loli.net/2020/02/21/DXp2byx59gqwMaF.png)

保存，然后 Windows 需要替换`C:/WINDOWS/system32/drivers/etc/hosts`这个文件为刚刚改动的文件，Linux 需要替换`/etc/hosts`这个文件为刚刚改动的文件，之后 Windows 可以在命令行工具中执行`ipconfig /flushdns`刷新 dns 缓存，Linux 需要执行`/etc/init.d/networking restart`重启网络，之后可以通过浏览器连接某些网站（如 p 站）来测试效果。
