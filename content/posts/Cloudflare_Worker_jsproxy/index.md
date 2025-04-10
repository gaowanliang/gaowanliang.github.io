---
title: "Cloudflare Worker + jsproxy实现浏览器代理"
date: 2019-11-04 20:54:38
summary: "CloudFlare 提供了一项很好用的服务 Worker，可以用来实现前端代理功能，jsproxy 是一个基于 Worker 的前端代理工具，可以实现浏览器代理访问其他网站。"
tags: ["Cloudflare", "jsproxy", "代理"]
categories: ["教程"]
---

# Cloudflare Worker+jsproxy 实现浏览器代理访问谷歌

## 简介

`CloudFlare Worker` 是 CloudFlare 的边缘计算服务。开发者可通过 JavaScript 对 CDN 进行编程，从而能灵活处理 HTTP 请求。这使得很多任务可在 CDN 上完成，无需自己的服务器参与。使用 jsproxy 技术就可以轻松进行前端代理功能，同时没有任何费用。

## 部署

首页：https://workers.cloudflare.com

注册，登陆，`Start building`，取一个子域名，`Create a Worker`。

复制 [index.js](https://raw.githubusercontent.com/EtherDream/jsproxy/master/cf-worker/index.js) 到左侧代码框，`Save and deploy`。如果正常，右侧应显示首页。

收藏地址框中的 `https://xxxx.子域名.workers.dev`，以后可直接访问。

我进行了美化，效果图如下：

![](https://i.loli.net/2019/11/12/qLJ5u1XRjr7EFCV.png)

## 计费

后退到 `overview` 页面可参看使用情况。免费版每天有 10 万次免费请求，对于个人通常足够。

如果不够用，可注册多个 Worker，在 `conf.js` 中配置多线路负载均衡。或者升级到 $5 的高级版本，每月可用 1000 万次请求（超出部分 $0.5/百万次请求）。

## 修改配置

默认情况下，静态资源从 `https://etherdream.github.io/jsproxy` 反向代理，可通过代码中 `ASSET_URL` 配置，从而可使用自定义的 `conf.js` 配置。

## 存在问题

- WebSocket 代理尚未实现

- 外链限制尚未实现

- 未充分测试，以后再完善
