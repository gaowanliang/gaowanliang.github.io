---
title: "【已过时】 一个极简的HTTP Server： Caddy一键安装脚本，带网站配置教程"
summary: "【已过时】 使用Caddy搭建一个极简的HTTP Server"
date: 2020-01-27 14:05:27
tags: ["建站", "Caddy", "转载"]
categories: ["教程"]
---

> [!WARNING]
> 此教程已过时，不再适用，仅供参考

**说明：**这几天一直在看逗比根据地，发现`Caddy`很是强大，是一个极简的`HTTP`服务器，支持`HTTP/2`静态网页服务器，安装也很快，在很多方面都可以不用安装时间很长的`lnmp`，`lamp`环境了，配置文件也很简单，同时还有一些有趣的扩展，可以非常方便的搭建一个`HTTP`服务器。

## 安装

**系统要求：**`CentOS 6+`/`Debian 6+`/`Ubuntu 14.04 +`。推荐`Debian 7 x64`。

本脚本只是一个一键安装+运行控制的脚本，没有其他管理虚拟主机等功能。

执行下面的代码安装`Caddy`，如果想要安装其他扩展可以把名字加到命令后面（`bash caddy_install.sh install xxx,xxx,xxx，`扩展列表[点击查看](https://caddyserver.com/download)）。

```
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
#备用地址
wget -N --no-check-certificate https://gwliang.com/caddy_install.sh && chmod +x caddy_install.sh && bash caddy_install.sh
```

## 使用说明

```
启动：/etc/init.d/caddy start
停止：/etc/init.d/caddy stop
重启：/etc/init.d/caddy restart
查看状态：/etc/init.d/caddy status
查看Caddy启动日志：tail -f /tmp/caddy.log
安装目录：/usr/local/caddy
Caddy配置文件位置：/usr/local/caddy/Caddyfile
Caddy自动申请SSL证书位置：/.caddy/acme/acme-v01.api.letsencrypt.org/sites/xxx.xxx(域名)/
```

## 升级 Caddy 或者更新扩展

只需要重新执行你当初安装时候用的命令即可，会覆盖安装最新的`Caddy+`扩展。

## 卸载 Caddy

卸载不会删除虚拟主机的内容，只会删除`Caddy`自身和配置文件。

```bash
wget -N --no-check-certificate https://raw.githubusercontent.com/ToyoDAdoubiBackup/doubi/master/caddy_install.sh && bash caddy_install.sh uninstall
#备用地址
wget -N --no-check-certificate https://gwliang.com/caddy_install.sh && bash caddy_install.sh uninstall
```

## 网站配置文件

配置文件为`Caddyfile`，列举几种`caddy`的`conf`的写法

**1、本地访问 80**

```
:80
```

**2、单网站访问**

```
example.com
root /usr/local/caddy/www
//更多模块
```

**2、多网站**

```
example.com {
  gzip
  root /usr/local/caddy/www
  //更多模块
}
```

常用基本组成模块，均写入到`conf`站点配置中。

```
#log日志
log /usr/local/caddy/www/example.log

#目录访问
browse

#gzip压缩
gzip

#自主ssl证书
tls /path/ssl/example.com.crt /path/ssl/example.com.key

#访问口令认证（用户emiria，密码abc123）
basicauth / emiria abc123

#跳转功能
redir http://example.com{url}

#自定义错误页面
errors {
  404 404.html
  500 /usr/local/caddy/www/500.html
}

```

例如，通过`IP`即`80`端口访问目录：

```
:80 {
  root /usr/local/caddy/www
  gzip
  browse
}
```

也可以直接执行命令写入，以下是一个命令，直接复制粘贴即可。

```
echo ":80 {
  root /usr/local/caddy/www
  gzip
  browse
}" > /usr/local/caddy/Caddyfile

```

更多配置参考：[https://caddyserver.com/tutorial/caddyfile](https://caddyserver.com/tutorial/caddyfile)
设置文档参考：[https://caddyserver.com/docs](https://caddyserver.com/docs)

## 常见问题

**1、Caddy 启动失败，打开 ip 访问地址显示的是 It works !**
一些系统会自带`apache2`，而`apache2`会占用`80`端口，导致`Caddy`无法绑定端口，所以只要关掉就好了。

```
netstat -lntp
# 我们可以通过这个命令查看是不是被其他软件占用了80端口。

```

不过`apache2`会默认开机自启动，如果不需要可以关闭自启动或者卸载`apache2`。

停止`Apache2`

```
# CentOS系统
/etc/init.d/httpd stop
# Debian/Ubuntu系统
/etc/init.d/apache2 stop

# 尝试使用上面代码关闭，如果没效果或者提示什么错误无法关闭，那就用下面这个强行关闭进程。
kill -9 $(ps -ef|grep "apache2"|grep -v "grep"|awk '{print $2}')

```

取消开机自启动

```
# CentOS 系统
chkconfig --del httpd
# Debian/Ubuntu 系统
update-rc.d -f apache2 remove

```

卸载`Apache2`

```
# CentOS 系统
yum remove httpd
# Debian/Ubuntu 系统
apt-get remove --purge apache2
```

关闭`Apache2`后，就可以尝试启动`Caddy`，并试试能不能打开网页。

```
/etc/init.d/caddy start
```

**2、启动 Caddy 后，无法访问**
这个可能是防火墙的问题，开放防火墙端口即可。

```
iptables -I INPUT -m state --state NEW -m tcp -p tcp --dport 端口 -j ACCEPT
iptables -I INPUT -m state --state NEW -m udp -p udp --dport 端口 -j ACCEPT

# 删除防火墙规则，内容一样把 -I 换成 -D 就行了：
iptables -D INPUT -m state --state NEW -m tcp -p tcp --dport 端口 -j ACCEPT
iptables -D INPUT -m state --state NEW -m udp -p udp --dport 端口 -j ACCEPT
```

**3、SSL 证书签发失败**
比如报错`acme: error: 400 :: urn:ietf:params:acme:error:connection :: Error getting validation data, url:`，检查下防火墙端口，需要开启`80`和`443`端口，然后重启即可。

## 转载声明

原文链接：https://www.moerats.com/archives/404/
