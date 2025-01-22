---
title: "LightUploader，一个轻量化的网盘上传工具，支持OneDrive国际/个人/家庭版、世纪互联、Google Drive"
date: 2021-04-29 16:40:07
tags: ["OneDrive", "网盘", "上传"]
categories: ["软件"]
---

```
更新
【2021.04.29】
目前支持世纪互联、Google Drive，预计后期会添加对Mega，Telegram等平台的支持

【2021.04.02】
目前同时支持国际版、个人版(家庭版)。

【2021.04.01】
修复授权失败、上传文件大小为0等问题。
现已支持arm、x86、x64平台的系统。

【2021.03.27】
新增多线程上传、MacOS客户端。
修复了一个致命bug，建议之前装过的更新下程序。

功能

支持上传文件和文件夹到指定目录，并保持上传前的目录结构。
支持命令参数使用，方便外部程序调用。
支持自定义上传分块大小。
支持多线程上传(多文件同时上传)。
支持根据文件大小动态调整重试次数，对抗不好的网络环境。
可以使用Telegram Bot实时监控上传进度
Github地址：https://github.com/gaowanliang/LightUploader
```

萌咖大佬写了一个 [非常好的版本](https://github.com/MoeClub/OneList/tree/master/OneDriveUploader) ，可惜并没有开源，而且已经好久都没有更新了。这个项目作为从 [DownloadBot](https://github.com/gaowanliang/DownloadBot) 中独立出来的一个简易上传工具，旨在用更轻量化的方式让在各种平台都能快速的向各个网络硬盘上传数据。

这里只详细说下 Linux 的用法，Windows 后面大概说下。

Windows、MacOS 系统下载地址 →[传送门](https://github.com/gaowanliang/LightUploader/releases)，直接将程序下载到本地后，按照下面方法进行授权、初始化，然后就可以使用命令上传了。

# 授权认证

点击右侧 URL 登录并授权，授权地址 →[【国际版、个人版(家庭版)】](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=ad5e65fd-856d-4356-aefc-537a9700c137&response_type=code&redirect_uri=http://localhost/onedrive-login&response_mode=query&scope=offline_access%20User.Read%20Files.ReadWrite.All)，
[【世纪互联】](https://login.chinacloudapi.cn/common/oauth2/v2.0/authorize?client_id=4fbf37cf-dc83-4b60-b6c1-6230546e247b&response_type=code&redirect_uri=http://localhost/onedrive-login&response_mode=query&scope=offline_access%20User.Read%20Files.ReadWrite.All)，Google Drive 的授权链接是实时生成的，下面会说。

授权后会获取一个 localhost 开头打不开的链接，这里复制好整个链接地址，包括 localhost。

# 安装 LightUploader

打开这个界面，选择适合你系统的版本：https://github.com/gaowanliang/LightUploader/releases

这里以 Linux 64 位为例，当前最新版本为 v2.0，应该下载 LightUploader_Linux_x86_64.tar.gz 这个文件

```bash
# 下载文件
wget https://github.com/gaowanliang/LightUploader/releases/download/v2.0/LightUploader_Linux_x86_64.tar.gz
# 解压文件
tar -zxvf LightUploader_Linux_x86_64.tar.gz -C /usr/local/bin/

# 给予权限
chmod +x /usr/local/bin/LightUploader

# 可选 软链接 类似于 Windows 的快捷方式
ln -s /usr/local/bin/LightUploader /usr/bin/LightUploader
```

# 初始化配置

```bash
# OneDrive 国际版，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader -a "url" -l zh-CN

# OneDrive 个人版(家庭版)，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader -a "url" -v 1 -l zh-CN

# OneDrive 中国版(世纪互联)，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader -a "url" -v 2 -l zh-CN

#Google Drive 是实时生成授权地址的，所以直接输入下面的命令然后按照指示操作即可。
LightUploader -v 3 -l zh-CN
```

如果提示`注册成功`类似信息，则初始化成功。

# 使用命令

```c
Usage of LightUploader:
  -a string
        // 初始化授权
        Setup and Init auth.json.
  -b string
        // 自定义上传分块大小, 可以提高网络吞吐量, 受限于磁盘性能和网络速度.
  -c string
        // 配置文件路径

  -r string
        // 上传到网盘中的某个目录, 默认: 根目录
  -l string
        // 软件语言
  -f string
        // *必要参数, 要上传的文件或文件夹
  -t string
        // 线程数, 同时上传文件的个数. 默认: 3
  -to int
        //单个数据包超时时间，默认为60s
  -tgbot string
        //使用Telegram机器人实时监控上传，此处需填写机器人的access token，形如123456789:xxxxxxxxx，输入时需使用双引号包裹。当写入内容为“1”时，使用配置文件中的BotKey和UserID作为载入项
  -uid string
        // 使用Telegram机器人实时监控上传，此处需填写接收人的userID，形如123456789
  -m int
        // 选择模式，0为替换网盘中同名文件，1为跳过，默认为0
  -v int
        // 选择版本，其中0为 OneDrive 国际版，1为 OneDrive 个人版(家庭版)，2为 OneDrive 世纪互联，3为Google Drive，默认为0
```

## 命令示例

```bash
# 一些示例:

# 将同目录下的 mm00.jpg 文件上传到 OneDrive 网盘根目录
LightUploader -c xxx.json -f "mm00.jpg"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录
LightUploader -c xxx.json -f "Download"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘Test目录中
LightUploader -c xxx.json -f "Download" -r "Test"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 10 线程
LightUploader -c xxx.json -t 10 -f "Download"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 10 线程，并跳过同名文件
LightUploader -c xxx.json -t 10 -f "Download" -m 1

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 10 线程，同时设置超时时间为30秒
LightUploader -c xxx.json -t 10 -f "Download" -to 30

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 10 线程，同时使用 Telegram Bot 实时监控上传进度
LightUploader -c xxx.json -t 10 -f "Download" -tgbot "123456:xxxxxxxx" -uid 123456789

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 10 线程，同时使用配置文件中的 Telegram Bot 参数载入程序实时监控上传进度（前提是配置文件中含有Telegram Bot 的参数）
LightUploader -c xxx.json -t 10 -f "Download" -tgbot "1"

# 将同目录下的 Download 文件夹上传到 OneDrive 网盘根目录中, 使用 15 线程, 并设置分块大小为 20M
LightUploader -c xxx.json -t 15 -b 20 -f "Download"

```

`/urs/local/auth.json`为初始化时，生成的\*.json 绝对路径地址，本文默认`/root/auth.json`，自行调整。

# Aria2 自动上传

同样的这里也会提供个配套的 Aria2 自动上传脚本
上传脚本代码如下：

```bash
#!/bin/bash

GID="$1";
FileNum="$2";
File="$3";
MaxSize="157286400";
Thread="3";  #默认3线程，自行修改，服务器配置不好的话，不建议太多
Block="20";  #默认分块20m，自行修改
RemoteDIR="";  #上传到Onedrive的路径，默认为根目录，如果要上传到Test目录，""里面请填成Test
LocalDIR="/www/download/";  #Aria2下载目录，记得最后面加上/
Uploader="/usr/local/bin/LightUploader";  #上传的程序完整路径，默认为本文安装的目录
Config="/root/auth.json";  #初始化生成的配置auth.json绝对路径，参考第3步骤生成的路径


if [[ -z $(echo "$FileNum" |grep -o '[0-9]*' |head -n1) ]]; then FileNum='0'; fi
if [[ "$FileNum" -le '0' ]]; then exit 0; fi
if [[ "$#" != '3' ]]; then exit 0; fi

function LoadFile(){
  if [[ ! -e "${Uploader}" ]]; then return; fi
  IFS_BAK=$IFS
  IFS=$'\n'
  tmpFile="$(echo "${File/#$LocalDIR}" |cut -f1 -d'/')"
  FileLoad="${LocalDIR}${tmpFile}"
  if [[ ! -e "${FileLoad}" ]]; then return; fi
  ItemSize=$(du -s "${FileLoad}" |cut -f1 |grep -o '[0-9]*' |head -n1)
  if [[ -z "$ItemSize" ]]; then return; fi
  if [[ "$ItemSize" -ge "$MaxSize" ]]; then
    echo -ne "\033[33m${FileLoad} \033[0mtoo large to spik.\n";
    return;
  fi
  ${Uploader} -c "${Config}" -t "${Thread}" -b "${Block}" -f "${FileLoad}" -r "${RemoteDIR}"
  if [[ $? == '0' ]]; then
    rm -rf "${FileLoad}";
  fi
  IFS=$IFS_BAK
}
LoadFile;
```

编辑好上传脚本后，可以检测下脚本编码是否正确，比如我脚本路径为`/root/upload.sh`，使用命令：

```bash
bash /root/upload.sh
```

如果无任何输出，则正确，反之输出类似$'r': command not found 错误，则需要转换下编码格式，具体步骤如下。

先安装 dos2unix：

```bash
#CentOS系统
yum install dos2unix -y

#Debian/Ubuntu系统
apt install dos2unix -y
再转换编码：

#后面为脚本路径
dos2unix /root/upload.sh
```

# Windows 使用

这里就随便补充下 Windows 使用，先下载程序文件，下载地址 →[传送门](https://github.com/gaowanliang/LightUploader/releases)。

比如我将 exe 文件放到 D 盘，然后使用 Win+R，输入 CMD 运行，调出窗口后，使用命令：

```cmd
#进入D盘
cd /d D:\

#国际版初始化，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader.exe -a "url" -l zh-CN

#个人版(家庭版)初始化，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader.exe -a "url" -v 1 -l zh-CN

#世纪互联初始化，将url换成你上面复制的授权地址，包括http://loaclhost。
LightUploader.exe -a "url" -v 2 -l zh-CN

```

然后上传命令和上面一样，只需要把`LightUploader`改成`LightUploader.exe`即可。

# 更换自己的 API

我本来想着做一个简单更换 API 的方法，但是思来想去不知道怎么弄好，因为本身这个软件是单文件的，首次就需要 API 进行登录，这个 API 存储在哪里就很麻烦了，索性就不弄了，和大家说一下如何通过自行编译的方式更换自己的 API。

使用 VSCode/Goland 等工具，进行全局替换，需要查找的值有这几个：

- OneDrive 国际版/家庭版 客户端 ID：`ad5e65fd-856d-4356-aefc-537a9700c137` (国际版不需要密钥)
- OneDrive 世纪互联 客户端 ID：`4fbf37cf-dc83-4b60-b6c1-6230546e247b`
- OneDrive 世纪互联 密钥：`y-L73QIBxO_UmJvOVw8YMlX~8B_h4D6zzT`
- Google Drive 授权文件：见[gdUpload.go#L224](https://github.com/gaowanliang/LightUploader/blob/main/googledrive/gdUpload.go#L224)

因为 GoogleDrive 还是测试版，目前传文件很不稳定，这里先简单的说一下 OneDrive 怎么弄

[Azure.com 应用注册](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)

[Azure.cn (世纪互联) 应用注册](https://portal.azure.cn/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)

没有说明的随意填写即可

选择 任何组织目录(任何 Azure AD 目录 - 多租户)中的帐户

重定向 url 键入并保存 http://localhost/onedrive-login

保存 `客户端ID`

去 证书和密码 ，添加并保存 `客户端密码`(国际版不需要)

API 权限（Microsoft Graph）添加 `offline_access`, `Files.ReadWrite.All`, `User.Read` 三个权限。

下载好源代码后，使用上面说的工具，按下`Ctrl+Shift+H`，根据自己的情况将上面的值直接全局替换即可，因为我软件的依赖库都直接绑到代码里了，所以装好 golang 直接`go build -o LightUploader .`编译即可，不会出现依赖问题。
