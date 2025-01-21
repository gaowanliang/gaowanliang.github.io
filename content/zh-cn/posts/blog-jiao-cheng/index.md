---
title: "高大上博客制作教程"
summary: "如何用Hexo搭建一个高大上的博客"
date: 2019-10-05 20:24:22
tags: ["博客", "Hexo", "Github"]
categories: ["教程"]
---

> [!WARNING]
> 此教程已过时，不再适用，仅供参考，本站已迁移至 Hugo

## 安装 Node.js

首先下载稳定版[Node.js](https://nodejs.org/dist/v9.11.1/node-v9.11.1-x64.msi)，我这里给的是 64 位的。

安装选项全部默认，一路点击`Next`。

最后安装好之后，按`Win+R`打开命令提示符，输入`node -v`和`npm -v`，如果出现版本号，那么就安装成功了。

## 添加国内镜像源

如果没有梯子的话，可以使用阿里的国内镜像进行加速。

```bash
npm config set registry https://registry.npm.taobao.org
```

## 安装 Git

为了把本地的网页文件上传到 github 上面去，我们需要用到分布式版本控制工具————Git[[下载地址]](https://git-scm.com/download/win)。

安装选项还是全部默认，只不过最后一步添加路径时选择`Use Git from the Windows Command Prompt`，这样我们就可以直接在命令提示符里打开 git 了。

安装完成后在命令提示符中输入`git --version`验证是否安装成功。

## 注册 Github 账号

接下来就去注册一个 github 账号，用来存放我们的网站。大多数小伙伴应该都有了吧，作为一个合格的程序猿（媛）还是要有一个的。

打开[https://github.com/](https://github.com/)，新建一个项目，输入自己的项目名字，后面一定要加`.github.io`后缀。**名称一定要和你的 github 名字完全一样，比如你 github 名字叫`abc`，那么仓库名字一定要是`abc.github.io`。**
然后项目就建成了，点击`Settings`，向下拉到最后有个`GitHub Pages`，点击`Choose a theme`选择一个主题。然后等一会儿，再回到`GitHub Pages`，在地址栏输入`你的用户名.github.io`就能看到你的主页了。
具体可以看我的[B 站视频](https://www.bilibili.com/video/av70212514)

## 安装 Hexo

在合适的地方新建一个文件夹，用来存放自己的博客文件，比如我的博客文件都存放在`D:\study\program\blog`目录下。

在该目录下右键点击`Git Bash Here`，打开 git 的控制台窗口，以后我们所有的操作都在 git 控制台进行，就不要用 Windows 自带的控制台了。

定位到该目录下，输入`npm i hexo-cli -g`安装 Hexo。会有几个报错，无视它就行。

安装完后输入`hexo -v`验证是否安装成功。

然后就要初始化我们的网站，输入`hexo init`初始化文件夹，接着输入`npm install`安装必备的组件。

这样本地的网站配置也弄好啦，输入`hexo g && hexo s`打开本地服务器，然后浏览器打开[http://localhost:4000/](http://localhost:4000/)，就可以看到我们的博客啦

按`ctrl+c`关闭本地服务器。

## 连接 Github 与本地

首先右键打开 git bash，然后输入下面命令：

```bash
git config --global user.name "你GitHub的用户名"

git config --global user.email "你GitHub注册时用的邮箱"

ssh-keygen -t rsa -C "你GitHub注册时用的邮箱"
```

默认回车即可，然后输入`cat C:\Users\(你电脑的用户名)\.ssh\id_rsa.pub`，然后把输出的东西整个复制出来
![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20191127204816.png)

打开[github](https://github.com)，在头像下面点击`settings`，再点击`SSH and GPG keys`，新建一个 SSH，Title 随便。

把刚才输出的一串复制到 key 里即可

输入`ssh -T git@github.com`，问 Are you sure you want to continue connecting (yes/no)?时输入 yes，如果出现你的用户名，那就成功了。

打开博客根目录下的`_config.yml`文件，这是博客的配置文件，在这里你可以修改与博客相关的各种信息。

修改最后一行的配置：

```bash
deploy:
  type: git
  repository: https://github.com/gaowanliang/gaowanliang.github.io
  branch: master
```

repository 修改为你自己的 github 项目地址。

## 写文章、发布文章

首先在博客根目录下右键打开 git bash，安装一个扩展`npm i hexo-deployer-git`。

然后输入`hexo new post "文章标题"`，新建一篇文章。

然后打开`D:\study\program\blog\source\_posts`的目录，可以发现下面多了一个文件夹和一个`.md`文件，一个用来存放你的图片等数据，另一个就是你的文章文件啦。

编写完 markdown 文件后，根目录下输入`hexo g`生成静态网页，然后输入`hexo s`可以本地预览效果，最后输入`hexo d`上传到 github 上。这时打开你的 github.io 主页就能看到发布的文章啦。

## 绑定域名

现在默认的域名还是`xxx.github.io`，是不是很没有牌面？想不想也像我一样弄一个专属域名呢，首先你得购买一个域名，xx 云都能买，看你个人喜好了。

以我的 Cloudflare 为例，如下图所示，添加这条解析记录：
![](https://i.loli.net/2019/10/06/CHx8L6cAZJ92RQ3.png)

然后打开你的 github 博客项目，点击`settings`，拉到下面`Custom domain`处，填上你自己的域名，保存：
![](https://i.loli.net/2019/10/06/5v7clsJBFQM9g1a.png)

这时候你的项目根目录应该会出现一个名为`CNAME`的文件了。如果没有的话，打开你本地博客`/source`目录，我的是`D:\study\program\blog\source`，新建`CNAME`文件，注意没有后缀。然后在里面写上你的域名，保存。最后运行`hexo g`、`hexo d`上传到 github。

## 备份博客源文件

有时候我们想换一台电脑继续写博客，这时候就可以将博客目录下的所有源文件都上传到 github 上面。

首先在 github 博客仓库下新建一个分支`hexo`，然后`git clone`到本地，把`.git`文件夹拿出来，放在博客根目录下。

然后`git checkout hexo`切换到`hexo`分支，然后`git add .`，然后`git commit -m &quot;xxx&quot;`，最后`git push origin hexo`提交就行了。

## 下载我弄好的主题包

大家也可以先用上文 hexo 安装方法安装完 hexo，然后直接[下载这个文件](https://cdn.jsdelivr.net/gh/gaowanliang/gitment-store/hexo-theme-matery.zip)，放到 theme 文件夹里，这是我修改完的基本没 bug 的定制化的博客，且用了[这篇博客](http://www.gwl6.me/2019/09/30/jia-su-github-pages-wang-zhan/)里说的加速方式，就不用你自己搞了，就可以直接拿来用啦。

## 其他定制选项

可以看主题包作者写的[这个](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)
