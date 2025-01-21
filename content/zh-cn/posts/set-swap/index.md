---
title: "Debian系统安装Swap虚拟内存设置"
date: 2020-05-16 20:31:37
tags: ["Debian", "Swap", "内存"]
categories: ["教程"]
---

我玩的服务器大多数内存比较小，有的时候执行一些任务会搞得 out of memory ，这个时候就需要设置虚拟内存，最好的方法就是用萌鼠大佬的一键脚本。

> wget https://www.moerats.com/usr/shell/swap.sh && bash swap.sh

但是有的时候执行完了会报错，这个时候就要麻烦一下，自己设置了

# 新增 swap 区

- 创建一个 文件作为 swap 区：

```bash
dd if=/dev/zero of=/swapfile1 bs=1024 count=2000000
```

名字为/swapfile1，大小为 bs*count = 1024*2000000=2G，count 代表的是大小，我这里是 2G。

- 将其转化为 swap 文件：

```
mkswap /swapfile1
```

- 将其改为只有 root 权限才能修改（这个可以不弄）

```bash
# chown root:root /swapfile1
# chmod 0600 /swapfile1
```

- 将其激活：

```bash
swapon /swapfile1
```

- 如果想要系统重启后生效，可以打开/etc/fstab 在最后面加上一行：

```bash
nano /etc/fstab

/swapfile1 swap swap defaults 0 0
```

# 更改 swap 区大小

在网上没找到更多办法，有一个傻办法：

1、通过上述的 1 ～ 5 步重建一个 swap 区，然后激活；

2、先通过 swapoff 命令将之前的 swap 区失效，然后 rm 掉：

```bash
swapoff /swapfile1
```
