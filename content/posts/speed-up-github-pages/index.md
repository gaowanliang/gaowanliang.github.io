---
title: "利用jsDelivr和Cloudflare免备案加速Github Pages网站"
date: 2019-09-30 09:20:30
tags: ["Github Pages", "加速", "jsdelivr", "Cloudflare"]
categories: ["教程"]
---

# 利用 jsDelivr 和 Cloudflare 免备案加速 Github Pages 网站

最近觉得学的东西不少，但是我从来没有记录过，最近正好因为新建了个博客，热乎劲还没过去，所以写点什么。

我比较穷，不想买个服务器，买了也浪费，同时又想写博客。还不想通过 CSDN，简书之类的“广告网站”来写东西（最近发现在手机上用 Chrome 浏览器看简书的内容的时候会有弹屏广告，而且关不掉，遂放弃了简书）。突然想起了好久之前看过的 Github Pages，于是照着网上的教程进行了梳理，最终创建了一个我还比较满意的博客。只是有一个很严重的缺陷，就是国内访问太慢了，查了很多教程，发现都是些接入国内 CDN 的教程，国内 CDN 不仅贵，而且你不备案是不能用的。对于我这样一个没啥需求的用户，还得做网站备案，太麻烦了。于是想能不能曲线救国，最终找到了一个比较有效，能提高 Github Pages 速度的方法，还不用花钱（可能要花点，但是不多）。

## 效果

先上效果（两个网页均是通过 **清空缓存并硬性重新加载** 方式来测试的）

![这是我用的主题的作者给的示例站，直到35000ms的时候才算是正式加载完成](https://i.loli.net/2019/09/29/f9ij6oYm83UpadG.png)

![这是本站加载速度，8000ms的时候就基本上加载完成](https://i.loli.net/2019/09/29/obNetxyrPZQ5fHR.png)

对于速度，大家可以自行测试，本站和 [blinkfox.github.io](https://blinkfox.github.io) 使用的是同一套模板，对方 9.9s 时还没跳到主页，我站 8s 就已经全部加载完成。那我做了什么改动呢？

## 第一步——压缩图片

刚开始的时候没有头绪，因为不知道 Github Pages 能换域名。我一想，图片是显示的大头，先从压缩图片开始做起，刚好有一个公司 [TinyPNG](https://tinypng.com/) 提供了接口。首先我们需要打开它的官网，点击上面的 _Developer API_，输入你的名字和 Email 就可以获得每月 500 次的免费试用 API 的资格，你放心，这个量对于我们这种写小博客的人来说是非常够用的。申请完了之后要写个压缩的小程序。对于我来说，做个小程序还是轻而易举的，我在这里使用 python 来简单写写。根据官方文档说明，你首先需要下载它的库

> pip install --upgrade tinify

然后就可以应用我这个遍历程序啦(突然发现被我删啦，重新写吧)

```python

import tinify
import os
tinify.key = "YOUR_API_KEY"


def tinify_all_pic(rootdir):
    _files = []
    list = os.listdir(rootdir)
    for i in range(0, len(list)):
        path = os.path.join(rootdir, list[i])
        if os.path.isdir(path):
            tinify_all_pic(path)
        if os.path.isfile(path):
            suffix = os.path.splitext(path)[-1]
            if suffix == '.png' or suffix == '.jpg':
                print(path)
                source = tinify.from_file(path)
                source.to_file(path)

tinify_all_pic(r"themes\hexo-theme-matery\source")
# 这里是用我存放资源的一个文件夹做示范，根据你们的文件夹不同进行相应的更换

```

压缩率通常在 50%以上（特别是你用超过 1M 以上的图片的时候），这样你在加载的时候不就提高 50%的加载速度了嘛 😂（不是）。

这样操作之后你的加载速度可以提高至少 20%以上（一些模板资源也被压缩了，有些图片没法用网页 URL 来代替）。

## 第二步——使用 jsDelivr 对博客静态资源进行静态加速

![jsDelivr](https://www.jsdelivr.com/img/logo-horizontal.svg)

jsDelivr 是一个很著名的 CDN 加速商，这个服务商只对你 GitHub 里的静态资源进行加速，但是不和 Cloudflare 一样免费提供全站加速，不过 Cf 在国内没有节点，这个公司在中国是依托网宿科技（QUANTIL）进行 CDN 加速。

![QUANTIL在国内的节点](https://i.loli.net/2019/09/30/65naHkyO2NFRWTe.png)

但是这也够了，也是提升我们速度的一个大杀器（Cloudflare 只是起辅助作用）。首先你需要先传一个你网站编译完后的稳定版本放在你的 GitHub 里，这样就可以进行加速了。我们只需要通过这样来引用你的资源

> https://cdn.jsdelivr.net/gh/user/repo@version/file

举个例子，如果我们直接引用网站的资源是这样引用的。

> https://github.com/gaowanliang/gaowanliang.github.io/blob/master/js/matery.js

其中 `gaowanliang` 是我的用户名，`gaowanliang.github.io` 是我的仓库名，那这样就可以进行套进去，效果就是这样:

> https://cdn.jsdelivr.net/gh/gaowanliang/gaowanliang.github.io/js/matery.js

甚至如果你是 js，css 文件，可以进行 min 化，哪怕你的仓库里没有 min 的文件

> https://cdn.jsdelivr.net/gh/gaowanliang/gaowanliang.github.io/js/matery.min.js

简单来说，如果你的文件放在 master 分支里，点开文件复制链接把链接里的 https://github.com/ 换成 https://cdn.jsdelivr.net/gh/ ，然后把 blob/master/ 删掉即可，然后就可以对你网站里的 js，css，图片大换特换啦。你比如说这个模板的作者把用到的 js，css 都写在了 `_config.yml` 里，我们就可以进行全部替换

![前提是你需要先传一个你网站编译完后的稳定版本放在你的GitHub里](https://i.loli.net/2019/09/30/Z2mdKjIeYJnSusi.png)

里面的图片也可以进行替换

![检测是不是本地，如果是本地就用本地图片，如果不是就使用jsdelivr加速](https://i.loli.net/2019/09/30/eEJ9BM3nFVcLfhI.png)

这样你的加载速度还能再上一个台阶。

## 第三步——使用 Cloudflare 进行全站加速

因为 Cf 在国内没有节点，所以可能出现不加速反而减速的效果，但是有总比没有好，这里就简单说一说。

首先你需要注册一个域名，国内国外都有相关的域名的注册商这里就不详述了，因为手头没有空闲的域名了，这里就用[这篇博客](https://starrycat.me/blog-add-cloudflare-cdn.html)和[这篇博客](https://blog.zfanw.com/github-pages-custom-domain/)的内容进行介绍。

### 注册 Cloudflare 账号

要使用 Cloudflare 的服务，我们先要到官网注册账号：[Cloudflare 注册](https://www.cloudflare.com/)

![](https://i.loli.net/2019/09/30/F4LKdBOuZAraGmb.png)

点击 Sign Up 注册，填写邮箱和密码即可。之后填上你要加速的博客主域名，然后点击 Add Site 添加。

![](https://i.loli.net/2019/09/30/yPjJDU2BmAWkHKa.png)
![](https://i.loli.net/2019/09/30/hMo7Psn1VLaQITU.png)

Cloudflare 在查询域名的 DNS 记录，点击 Next 继续，选择免费套餐

![](https://i.loli.net/2019/09/30/y7eJdDb35YMkO94.png)

![](https://i.loli.net/2019/09/30/N4iItqoAxKXQZsY.png)

这里我们是免费套餐，可以无视直接点击 Confirm 确认。

![](https://i.loli.net/2019/09/30/CmZ9h3xfajpMesw.png)

### 修改 DNS 域名服务器

这里是域名的 DNS 记录查询结果，如有遗落，可自行添加。建议所有全部访问都通过 CDN 加速，其余默认，确认后继续。

![](https://i.loli.net/2019/09/30/kiNjrDAHJTfV6Rm.png)

然后去你的域名提供商修改默认域名服务器为 Cloudflare 提供的，修改后继续，更改成功需要几分钟或更长的时间。时不时 Check 一下。

![](https://i.loli.net/2019/09/30/9Amja7kMRKbTNp2.png)

### 更换到你的自定义域名

首先我们需要在仓库的设置里填入自定义域名，如下图：

![](https://i.loli.net/2019/09/30/WTPCJ4iv6Eq8gSw.png)

此时访问 example.zfanw.com，浏览器会提示：

> example.zfanw.com’s server IP address could not be found.

IP 地址未找到 - 因为我们还没做任何 DNS 解析的工作。

### 绑定 CNAME

一般情况下，我们是添加 A 记录将域名指向 IP。但 Github Pages 是通过 CNAME 绑定的 - CNAME 指 Canonical Name record，其实就是域名的别名。

在 DNS 管理中添加一条 CNAME 记录，将 example 子域名指向 chenxsan.github.io

然后就成功了

## 享受一下吧

经过加速后你的网站就可以达到我说的速度啦，虽然没有很快，但是在免备案的情况下达到这个速度已经很好了（总比 10s 才相应网页好吧）。当然我还在想办法继续优化，有新方法还会更新的。
