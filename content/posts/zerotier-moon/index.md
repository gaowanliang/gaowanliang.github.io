---
title: "ZeroTier-One在Linux搭建moon完整过程"
date: 2021-04-25 16:17:23
tags: ["内网穿透", "zerotier"]
categories: ["教程"]
---

## 安装配置 ZeroTier 客户端

执行命令： `curl -s https://install.zerotier.com/ | sudo bash`

运行完后即安装成功，界面会出现如图
![](https://cdn.jsdelivr.net/gh/gaowanliang/p/img/20210425163800.png)

## 启动安装好的 ZeroTier

```bash
sudo systemctl start zerotier-one.service

sudo systemctl enable zerotier-one.service
```

将安装好 ZeroTier 的加入你事先注册好的 ZeroTier 虚拟局域网中

执行命令：`sudo zerotier-cli join aaaaaaaaaaaa`

此处的`aaaaaaaaaaaa`是本人 ZeroTier 虚拟局域网的 ID，请更改为你本人自己的 ID

然后去 zerotier 管理页面，对加入的设备进行打钩

## 搭建 ZeroTier 的 Moon 中转服务器，生成 moon 配置文件

执行命令：`cd /var/lib/zerotier-one/`

执行命令：`sudo zerotier-idtool initmoon identity.public > moon.json`

修改配置文件 moon.json，（主要是添加公网 IP，公网 IP 是服务器的 IP，9993 是 zerotier 的默认端口，你服务器防火墙上需要开放 UDP:9993,否则是连接不上 Moon 的）

执行命令：`nano moon.json`

修改 stableEndpoints，

```jsonc
{
  //...
  "stableEndpoints": ["1.1.1.1/9993"]
  //...
}
```

此处的`1.1.1.1`就是公网 IP，这你自己服务器的 IP 地址

按`Ctrl+X`退出编辑

## 生成签名文件

执行命令：`zerotier-idtool genmoon moon.json`

执行之后会生产一个`000000xxxxxxxx.moon`的文件，将这个文件下载本地，`xxxxxxxx`是随机生成的，记住这个后面要用

将 moon 节点加入网络。创建`moons.d`文件夹，并把签名文件移动到文件夹内

执行命令：`sudo mkdir moons.d`

执行命令：`sudo mv 000000xxxxxxxx.moon moons.d/`

此处的`00000xxxxxxxxx.moon`是上一步生成的文件名，请改成你自己本人的。

重启中转服务器的 zerotier-one ：

执行命令：`sudo systemctl restart zerotier-one`

到这里，服务器的 moon 就配置完成了。
对客户端安装 zerotier 后，将配置好的 moon 文件配置到客户端，并重启 zerotier 完成与 moon 的连接。

## 客户端配置

### Linux

使用之前步骤中 `moon.json` 文件中的 id 值 (10 位的字符串，就是 xxxxxx），不知道的话在服务器上执行如下命令可以得到 id。

执行命令：`grep id /var/lib/zerotier-one/moon.json | head -n 1`

然后在客户端机器里执行命令：

执行命令：`zerotier-cli orbit xxxxxxx xxxxxxx`

此处的`xxxxxxx`刚刚在服务器得到的 ID 值

### Windows

打开服务程序`services.msc`, 找到服务`ZeroTier One`, 并且在属性内找到该服务可执行文件路径,并且在其下建立 moons.d 文件夹,然后将 moon 服务器下生成的`000xxxx.moon`文件，拷贝到此文件夹内。再重启该服务即可(计算机右键管理-找到服务双击打开-找到`zerotier one`右键重新启动即可)

路径一般是 Windows: `C:\ProgramData\ZeroTier\One`

## Android

Android 的官方客户端是不能使用 moon 的，但是可以用魔改版做到
地址 https://github.com/kaaass/ZerotierFix

去 [releases](https://github.com/kaaass/ZerotierFix/releases) 下载最新版本后，点击右上角三个点，点击入轨，之后点击右下角加号，然后选择从文件导入，将事先准备好的 moon 文件导入，即可使用 moon 链接各个设备了。

## 测试是否成功

（客户端 cmd 运行）若有出现你的服务器 IP 地址,即可证明 moon 连接成功

执行命令：`zerotier-cli listpeers`
