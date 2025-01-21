---
title: "Linux命令行下载OneDrive分享链接中的文件"
date: 2020-05-26 22:24:24
tags: ["OneDrive", "Linux", "下载"]
categories: ["教程"]
---

我所知的某个游戏分享站喜欢用 OneDrive 来分享游戏，用 OneDrive 很好，抵制百度网盘我强烈支持，但是 OneDrive 国内有时候抽风比较厉害，而在国外 OneDrive 下载几乎能跑满带宽，我 G 口的服务器可以跑到 110M/s，所以用国外服务器下载，然后再下载自己服务器里的东西会比较好。

但是因为 OneDrive 有些坑，无法从分享界面获取真实下载链接，比如下面这个链接

> https://gitaccuacnz2-my.sharepoint.com/:f:/g/personal/mail_finderacg_com/EheQwACFhe9JuGUn4hlg9esBsKyk5jp9-Iz69kqzLLF5Xw?e=FG7SHh

点开之后，找个文件点击下载，会得到这样的下载链接：

> https://gitaccuacnz2-my.sharepoint.com/personal/mail_finderacg_com/_layouts/15/download.aspx?UniqueId=cb44677f%2Dc4af%2D44ad%2D88d4%2Dceb07d9625da

但是如果直接在服务器里 wget 这个下载链接，100%会 403: Forbidden

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200526224303.png)

有经验的老手都知道肯定是没带 Cookie ，但是要怎么带 Cookie 呢，其实很简单

请使用 Chrome 或者是 Firefox，实测 Firefox 最快捷，不用进行更改，我下面就用 Firefox 进行演示。

首先你需要按下 F12，打开开发人员工具，然后转到网络选项，之后点击想要下载的文件进行下载，同时观察开发人员工具窗口，找到带有 download.aspx/?.... 的那个链接

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200526224906.png)

之后在那个链接上右键，选择 复制-复制为 cURL 命令(POSIX)

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200526225150.png)

会在剪切板出现类似下面的命令语句

```
curl 'https://gitaccuacnz2-my.sharepoint.com/personal/mail_finderacg_com/_layouts/15/download.aspx?UniqueId=cb44677f%2Dc4af%2D44ad%2D88d4%2Dceb07d9625da' -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:78.0) Gecko/20100101 Firefox/78.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Accept-Language: zh-HK,zh-TW;q=0.5' --compressed -H 'Connection: keep-alive' -H 'Referer: https://gitaccuacnz2-my.sharepoint.com/personal/mail_finderacg_com/_layouts/15/onedrive.aspx?.......' -H 'Cookie: CCSInfo=NS8yOS8yMDIwIDEwOjE1OjE0IEFNC/.....; FeatureOverrides_enableFeatures=; FeatureOverrides_disableFeatures=' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: iframe' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: same-origin'
```

之后粘贴到 Linux 的命令行里，最后在后面补加一句 `--output file.extension` ,其中 `file.extension` 是想要保存的文件名。最终执行的命令就是类似这样的：

```
curl 'https://gitaccuacnz2-my.sharepoint.com/personal/mail_finderacg_com/_layouts/15/download.aspx?UniqueId=cb44677f%2Dc4af%2D44ad%2D88d4%2Dceb07d9625da' -H 'User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:78.0) Gecko/20100101 Firefox/78.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8' -H 'Accept-Language: zh-HK,zh-TW;q=0.5' --compressed -H 'Connection: keep-alive' -H 'Referer: https://gitaccuacnz2-my.sharepoint.com/personal/mail_finderacg_com/_layouts/15/onedrive.aspx?.......' -H 'Cookie: CCSInfo=NS8yOS8yMDIwIDEwOjE1OjE0IEFNC/.....; FeatureOverrides_enableFeatures=; FeatureOverrides_disableFeatures=' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: iframe' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: same-origin' --output file.extension
```

![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20200526225910.png)

最后就能享受高速下载了

同时这个方法也适合文件夹下载，只不过前缀是 `xxxxx-mediap.svc.ms` ，其中`xxxxx`是地区代号，不同的下载链接所指向的地区不同，具体看情况，这里就不再赘述。

如果使用 Chrome 下载，需要选择 复制 cURL(Bash) 然后把里面的换行和\ 都处理掉，要不然会不行，但是我没成功过，按理讲应该是能成功的，算了，Firefox 能用就行了，又不是天天下载。
