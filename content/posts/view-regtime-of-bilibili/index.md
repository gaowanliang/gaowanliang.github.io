---
title: "[已过时] 查看你在B站注册的时间"
date: 2020-02-12 11:01:24
tags: ["B站", "注册时间"]
categories: ["工具"]
---

# 查看你在 B 站注册的时间

**请按步骤操作**

登录你的 B 站账号（如果你在客户端里打开那默认就是已经登录了）

## 点击下面的网页

> https://member.bilibili.com/x2/creative/h5/calendar/event?ts=0

<div id="open" class="mdui-container" align=center>
</div>

**复制中间部分（或者全部复制,电脑端建议全部复制）**

手机端长按`vip` 或 `identification` 就能选中那一大块

<div align=center>
<img src="https://cdn.jsdelivr.net/gh/gaowanliang/p/img/Screenshot_2020-02-12-11-14-47-830_tv.danmaku.bil.jpg" height=256 /></div>

如果你是手机端，请使用回退键（或者全面屏的手势），不要点击上面的 ×，这样你要重新再点进来

## 粘贴到下面的输入框中

<div class="mdui-container">
<div class="mdui-textfield mdui-textfield-floating-label">
  <label class="mdui-textfield-label">请输入复制的代码</label>
  <input id="jsona" class="mdui-textfield-input" type="text"/>
</div>
</div>

**你的注册时间是** <span id="TAT"></span>

<p id="QAQ"></p>

## 原理

刚才取出的数据中，其中有一段是`"jointime":******`，后面跟的就是你的注册时间，只不过这个是时间戳，我原来的视频里讲到过。

时间戳是指格林威治时间 1970 年 01 月 01 日 00 时 00 分 00 秒(北京时间 1970 年 01 月 01 日 08 时 00 分 00 秒)起至现在的总毫秒数。（现在取出来的是秒时间戳）经过换算就能得出你具体的注册时间

## 声明

本网站为静态网站，不会记录你的任何信息，请放心使用

<script>
var timestamp=0,sign=false
$("head").prepend('<link rel="stylesheet" href="//cdnjs.cloudflare.com/ajax/libs/mdui/0.4.3/css/mdui.min.css">');$("head").prepend('<meta name="referrer" content="no-referrer"/>');$.getScript("//cdnjs.cloudflare.com/ajax/libs/mdui/0.4.3/js/mdui.min.js")
$.getScript("https://cdn.jsdelivr.net/gh/gaowanliang/p/img/moment.min.js",function(){$.getScript("https://cdn.jsdelivr.net/gh/codebox/moment-precise-range/moment-precise-range.min.js");$("#open").html(`<button onclick="openWin()" id="openInThisWin" class="mdui-btn mdui-btn-raised mdui-ripple mdui-color-purple">在本页面打开</button>
<div style="display: none;" id="xx"><iframe src="https://member.bilibili.com/x2/creative/h5/calendar/event?ts=0" frameborder="1/0" width="100%" scrolling="yes/no/auto"></iframe><p>手机端需要自己找到"level"、"vip"、"identification"位置，长按其中任意一个单词，复制即可；</p><p>电脑端点击代码窗口后，按下Ctrl+A然后按下Ctrl+C即可全部复制</p></div>`)});function transformTime(timestamp){if(timestamp){var time=new Date(timestamp);var y=time.getFullYear();var M=time.getMonth()+1;var d=time.getDate();var h=time.getHours();var m=time.getMinutes();var s=time.getSeconds();return y+'-'+addZero(M)+'-'+addZero(d)+' '+addZero(h)+':'+addZero(m)+':'+addZero(s);}else{return'';}}
function addZero(m){return m<10?'0'+m:m;}
$("#jsona").bind("input propertychange",function(event){if((/ime":(.*?),/g).exec($("#jsona").val())!=null){timestamp=parseInt((/ime":(.*?),/g).exec($("#jsona").val())[1])*1000
$("#TAT").text(transformTime(timestamp))
if(sign==false){sign=true
guole()}}});function guole(){if((/ime":(.*?),/g).exec($("#jsona").val())!=null){var starts=moment(transformTime(timestamp));var ends=moment();var diff=moment.preciseDiff(starts,ends,true)
$("#QAQ").text(`你已经注册了${diff.years}年${diff.months}个月${diff.days}天${diff.hours}小时${diff.minutes}分钟${diff.seconds}秒了`)
setTimeout("guole()",1000);}else{sign=false
return false}}
function openWin(){$("#openInThisWin").hide();$("#xx").show();}
</script>
