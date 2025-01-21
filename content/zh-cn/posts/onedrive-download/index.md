---
title: "在纯命令行环境下批量下载OneDrive/SharePoint分享链接中的文件"
date: 2021-05-08 09:13:31
tags: ["OneDrive", "SharePoint", "Aria2"]
categories: ["教程"]
---

前一段时间我发了一篇文章，是关于[Linux 命令行下载 OneDrive 分享链接中的文件](https://gwliang.com/2020/05/26/linux-control-download-onedrive-files/)的，这篇文章其实解决了一些痛点，在没有图形化界面的 Linux 上终于可以无痛下载 OneDrive 分享的文件而不是只能在有图形化界面的系统上用浏览器下载。但是虽然解决了一些痛点，但是还没彻底解决，还有一些痛点如下

- 只能使用 curl 下载（虽然可以用其他工具下载，但是还得一条一条复制，麻烦）
- 需要自己打文件名
- 不能多线程下载
- 仍然需要一个图形化界面同时可以调试的终端

但是这些痛点还不算痛了，总有办法解决，这里就给出了一个解决方案， https://github.com/gaowanliang/OneDriveShareLinkPushAria2 ，新轮子实现了使用 python 环境，自动解析下载链接并推送到 Aria2 下载，使用方法如下：

程序一共有两个函数，`getFiles`和`downloadFiles`，由于程序是两个小时摸鱼摸出来的，所以写的很糙，不过能用就好。**需要翻到程序最后**。

`getFiles`自不用说，是获取链接下文件的目录，参数只有一个，也就是下载链接

`downloadFiles`是获取链接下文件下载链接并推送到 Aria2，这里需要填写 3 个参数，第一个是下载链接；第二个是 aria2RPC 的地址，这个一般就是改两个地方，就是 IP 和端口，`localhost`改成 aria2 所在位置的 IP，如果是本机就不用管了，冒号后面是端口，一般是 6800，我代码给的是 5800，记得修改；第三个是 aria2 的密钥，这个也不用详细说了，改好之后直接运行即可，此时所有的文件都会被推送过去。

现在剩下的痛点就是如果链接里有文件夹的话，目前还没解析，同时如果链接有密码也不能下载，慢慢解决吧。

最后记录一下，aria2 的 header 居然是字符串形式的，形如`accept: application/json\naccept-encoding: gzip, deflate, br\naccept-language: zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6`，一定要注意不要被坑
