---
title: "使用 acme.sh 申请 Let's Encrypt 泛域名SSL证书详细教程"
summary: "acme.sh是一个实现了acme协议的脚本，可以从Let's Encrypt生成免费的SSL证书"
date: 2020-02-04 20:08:11
tags: ["SSL"]
categories: ["教程"]
---

## acme.sh

`acme.sh`是一个实现了 acme 协议的脚本，可以从 Let's Encrypt 生成免费的 SSL 证书

本文只给出配合解析商 API 申请泛域名证书的过程教程，acme.sh 还有很多很强大的功能例如配合 Nginx 或者 Apache 等自动申请证书，请自行查看 github 项目 wiki 发掘

https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E

## 部署 acme.sh

```bash
curl https://get.acme.sh | sh
# 或者
wget -O- https://get.acme.sh | sh
```

## 获取 Cloudflare 的 API 信息

acme.sh 目前支持 cloudflare, dnspod, cloudxns, godaddy 以及 ovh 等数十种解析商的 API，可以自动添加 TXT 记录来验证，不需要人工操作，十分便捷

详细可以看
https://github.com/Neilpang/acme.sh/blob/master/dnsapi/README.md

这里以 Cloudflare 为例，CloudFlare Domain API 提供了两种自动颁发证书的方法。

### 使用全局 API 密钥

首先，您需要登录到 CloudFlare 帐户以获取[全局 API 密钥](https://dash.cloudflare.com/profile)。

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200204201527.png)

然后根据你的 API 密钥来填写下面的命令，并在服务器里执行

```
export CF_Key="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Email="xxxx@sss.com"
```

其中`CF_Key`是你的 Global API Key，`CF_Email`是你注册 cloudflare 的邮箱。

### 使用新的 cloudflare API 令牌

```
export CF_Token="sdfsdfsdfljlbjkljlkjsdfoiwje"
export CF_Account_ID="xxxxxxxxxxxxx"
```

没试过，只会第一种

其他解析商的环境变量名是不一样的，详情去这里看
https://github.com/Neilpang/acme.sh/blob/master/dnsapi/README.md

这个临时环境变量只需配置这一次，当成功申请证书后，API 信息会被自动保存在~/.acme.sh/account.conf，下次你使用 acme.sh 的时候会被自动使用

## 开始申请证书

接下来正式开始申请泛域名证书，将 example.com 换成你自己的域名

```
~/.acme.sh/acme.sh --issue --dns dns_cf -d example.com -d '*.example.com'
```

--dns dns_cx 参数表示使用 CloudXNS 的 API，如果是其他解析商，同样的，从上面那个链接可以看到对应参数

然后静静等待即可

## 得到证书

申请完成后屏显会输出证书路径

证书是`fullchain.cer`
密匙是`example.com.key`

由于证书默认（在你没安装宝塔等同样调用 acme 签发证书的面板时）会在 acme 的内部目录`~/.acme.sh/`中生成，不建议直接使用该路径填写至 nginx 等配置中，而是使用`--installcert`命令，具体请参考 ↓

https://github.com/Neilpang/acme.sh/wiki/%E8%AF%B4%E6%98%8E#3-copy%E5%AE%89%E8%A3%85-%E8%AF%81%E4%B9%A6

## 关于证书续期

Let's Encrypt 证书的有效期为三个月，`acme.sh`会每隔 60 天自动帮你续期，无需你进行任何干预

如果你想强制续期或者取消自动续期可以看
https://github.com/Neilpang/acme.sh#11-how-to-renew-the-certs
