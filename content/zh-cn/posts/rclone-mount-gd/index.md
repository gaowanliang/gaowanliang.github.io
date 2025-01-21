---
title: 在Debian/Ubuntu上使用rclone挂载Google Drive网盘
date: 2020-12-02 10:56:07
tags: ["Linux", "Google Drive", "Debian", "rclone", "转载"]
categories: ["教程"]
---

# 安装

## 安装 rclone

```bash
wget https://gwliang.com/rclone_debian.sh && bash rclone_debian.sh
```

## 获取 API

首先获取谷歌 API 凭据，先启用 Google Drive API，启用地址：[【点击进入】](https://console.developers.google.com/apis/api/drive.googleapis.com/overview)。
[![DI17wj.png](https://s3.ax1x.com/2020/12/02/DI17wj.png)](https://imgchr.com/i/DI17wj)
再创建一个`OAuth client ID`，创建地址：[【点击进入】](https://console.developers.google.com/apis/credentials/oauthclient)。应用类型选择`桌面程序`，名称自己填，这里博主我就保持默认。

[![DIG6w4.png](https://s3.ax1x.com/2020/12/02/DIG6w4.png)](https://imgchr.com/i/DIG6w4)

之后将出现的`客户端 ID`和`客户端密钥` 准备好，接下来会用到。

## 初始化配置

```bash
rclone config
```

会出现以下信息：

```bash
No remotes found - make a new one
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n
name> gwl # 配置名称，随便填
Type of storage to configure.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / 1Fichier
   \ "fichier"
 2 / Alias for an existing remote
   \ "alias"
 3 / Amazon Drive
   \ "amazon cloud drive"
 4 / Amazon S3 Compliant Storage Provider (AWS, Alibaba, Ceph, Digital Ocean, Dreamhost, IBM COS, Minio, Tencent COS, etc)
   \ "s3"
 5 / Backblaze B2
   \ "b2"
 6 / Box
   \ "box"
 7 / Cache a remote
   \ "cache"
 8 / Citrix Sharefile
   \ "sharefile"
 9 / Dropbox
   \ "dropbox"
10 / Encrypt/Decrypt a remote
   \ "crypt"
11 / FTP Connection
   \ "ftp"
12 / Google Cloud Storage (this is not Google Drive)
   \ "google cloud storage"
13 / Google Drive
   \ "drive"
14 / Google Photos
   \ "google photos"
15 / Hubic
   \ "hubic"
16 / In memory object storage system.
   \ "memory"
17 / Jottacloud
   \ "jottacloud"
18 / Koofr
   \ "koofr"
19 / Local Disk
   \ "local"
20 / Mail.ru Cloud
   \ "mailru"
21 / Mega
   \ "mega"
22 / Microsoft Azure Blob Storage
   \ "azureblob"
23 / Microsoft OneDrive
   \ "onedrive"
24 / OpenDrive
   \ "opendrive"
25 / OpenStack Swift (Rackspace Cloud Files, Memset Memstore, OVH)
   \ "swift"
26 / Pcloud
   \ "pcloud"
27 / Put.io
   \ "putio"
28 / QingCloud Object Storage
   \ "qingstor"
29 / SSH/SFTP Connection
   \ "sftp"
30 / Sugarsync
   \ "sugarsync"
31 / Tardigrade Decentralized Cloud Storage
   \ "tardigrade"
32 / Transparently chunk/split large files
   \ "chunker"
33 / Union merges the contents of several upstream fs
   \ "union"
34 / Webdav
   \ "webdav"
35 / Yandex Disk
   \ "yandex"
36 / http Connection
   \ "http"
37 / premiumize.me
   \ "premiumizeme"
38 / seafile
   \ "seafile"
Storage> 13 #选择13，Google Drive，注意该序列号会随时变化，看清楚再填
** See help for drive backend at: https://rclone.org/drive/ **

Google Application Client Id
Setting your own is recommended.
See https://rclone.org/drive/#making-your-own-client-id for how to create your own.
If you leave this blank, it will use an internal key which is low performance.
Enter a string value. Press Enter for the default ("").
client_id> xxxxxx.apps.googleusercontent.com # 这里输入客户端 ID
OAuth Client Secret
Leave blank normally.
Enter a string value. Press Enter for the default ("").
client_secret> xxxxxxxx # 这里输入客户端密钥
Scope that rclone should use when requesting access from drive.
Enter a string value. Press Enter for the default ("").
Choose a number from below, or type in your own value
 1 / Full access all files, excluding Application Data Folder.
   \ "drive"
 2 / Read-only access to file metadata and file contents.
   \ "drive.readonly"
   / Access to files created by rclone only.
 3 | These are visible in the drive website.
   | File authorization is revoked when the user deauthorizes the app.
   \ "drive.file"
   / Allows read and write access to the Application Data folder.
 4 | This is not visible in the drive website.
   \ "drive.appfolder"
   / Allows read-only access to file metadata but
 5 | does not allow any access to read or download file content.
   \ "drive.metadata.readonly"
scope> 1 # 这里输入1，完全访问所有文件，不包括应用程序数据文件夹。
ID of the root folder
Leave blank normally.

Fill in to access "Computers" folders (see docs), or for rclone to use
a non root folder as its starting point.

Enter a string value. Press Enter for the default ("").
root_folder_id>
Service Account Credentials JSON file path
Leave blank normally.
Needed only if you want use SA instead of interactive login.

Leading `~` will be expanded in the file name as will environment variables such as `${RCLONE_CONFIG_DIR}`.

Enter a string value. Press Enter for the default ("").
service_account_file>
Edit advanced config? (y/n)
y) Yes
n) No (default)
y/n> n # 输入n
Remote config
Use auto config?
 * Say Y if not sure
 * Say N if you are working on a remote or headless machine
y) Yes (default)
n) No
y/n> n # 输入n
Please go to the following link: https://accounts.google.com/o/oauth2/auth?xxxxxx
# 这里进入提示的链接，复制验证码
Log in and authorize rclone for access
Enter verification code> 4/1AY0e- # 这里输入验证码
Configure this as a team drive?
y) Yes
n) No (default)
y/n> n # 输入n
--------------------
[gwl]
type = drive
client_id = xxxxx.apps.googleusercontent.com
client_secret = xxxxx
scope = drive
token = {"access_token":"ya29.a0AfH6SMD66Wxxxx","token_type":"Bearer","refresh_token":"1//09SDe5e04DxQrCgYIARAAGAkSNwF","expiry":"2020-12-02T10:44:56.526147875Z"}
--------------------
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y
Current remotes:

Name                 Type
====                 ====
gwl                  drive

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q # 输入q保存退出
```

## 挂载为磁盘

#新建本地文件夹，路径自己定，即下面的 LocalFolder

```bash
mkdir /root/GoogleDrive
# 挂载为磁盘，下面的DriveName、Folder、LocalFolder参数根据说明自行替换
rclone mount DriveName:Folder LocalFolder --copy-links --no-gzip-encoding --no-check-certificate --allow-other --allow-non-empty --umask 000
```

`DriveName`为初始化配置填的`name`，`Folder`为`Google Drive里的文件夹`，`LocalFolder`为 VPS 上的`本地文件夹`。

比如高玩梁按照刚才的操作挂载指令就是：

```
rclone mount gwl:/ /root/GoogleDrive --copy-links --no-gzip-encoding --no-check-certificate --allow-other --allow-non-empty --umask 000
```

挂载成功后，输入`df -h`命令查看即可！

[![DIYEUH.png](https://s3.ax1x.com/2020/12/02/DIYEUH.png)](https://imgchr.com/i/DIYEUH)

# 开机自启

先新建 systemd 配置文件，适用 CentOS 7、Debian 8+、Ubuntu 16+。

再使用命令：

```bash
# 将后面修改成你上面手动运行命令中，除了rclone的全部参数
command="mount DriveName:Folder LocalFolder --copy-links --no-gzip-encoding --no-check-certificate --allow-other --allow-non-empty --umask 000"
#以下是一整条命令，一起复制到SSH客户端运行
cat > /etc/systemd/system/rclone.service <<EOF
[Unit]
Description=Rclone
After=network-online.target

[Service]
Type=simple
ExecStart=$(command -v rclone) ${command}
Restart=on-abort
User=root

[Install]
WantedBy=default.target
EOF
```

## 开始启动：

```bash
systemctl start rclone
```

## 设置开机自启：

```
systemctl enable rclone
```

## 其他命令：

```
重启：systemctl restart rclone
停止：systemctl stop rclone
状态：systemctl status rclone
```

如果你想挂载多个网盘，那么将 systemd 配置文件的`rclone.service`改成`rclone1.service`即可，重启动什么的同样换成`rclone1`。
