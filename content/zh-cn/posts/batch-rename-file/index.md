---
title: "如何优雅的批量重命名文件"
summary: "批量重命名挺麻烦的，所以我顺手整理了一下有哪些好方法可以批量重命名的"
date: 2020-11-22 12:53:47
tags: ["教程", "工具"]
categories: ["工具"]
---

# 批量给文件添加前后缀

## 前缀

如果你需要给某个类型的文件加上前缀，请参考下面的代码

```bash
for /f "delims=" %%f in ('dir /a /b *.mp3') do (
    ren "%%f" "N1-%%~nxf"
)
pause
```

其中`.mp3`是你需要修改的后缀名，而`N1-`是你需要添加的前缀

## 后缀

如果你需要给某个类型的文件加上后缀，请参考下面的代码

```bash
for /f "delims=" %%f in ('dir /a /b *.mp3') do (
    ren "%%f" "%%~nf-N2%%~xf"
)
pause
```

其中`.mp3`是你需要修改的后缀名，而`-N2`是你需要添加的后缀

# 批量按顺序重命名

如果你需要给文件按顺序重命名，请参考下面的代码

## 文件

### 前缀

```bash
set a=0

setlocal EnableDelayedExpansion

for /f "delims=" %%f in ('dir /a /b *.*') do (
    if not "%%~nxf"=="%~nx0" (
        set /A a+=1
        ren "%%f" "动漫!a!%%~xf"
    )
)
pause
```

其中`动漫`是每个文件所拥有的前缀

### 后缀

```bash
set a=0

setlocal EnableDelayedExpansion

for /f "delims=" %%f in ('dir /a /b *.*') do (
    if not "%%~nxf"=="%~nx0" (
        set /A a+=1
        ren "%%f" "!a!动漫%%~xf"
    )
)
pause
```

其中`动漫`是每个文件所拥有的后缀

## 文件夹

### 前缀

```bash
set a=1000
setlocal EnableDelayedExpansion
for /f "tokens=*" %%i in ('dir /a:d /b') do (
    set /A a+=1
    ren "%%i" "电影!a!"
)
```

其中`电影`是每个文件夹所拥有的前缀

### 后缀

```bash
set a=1000
setlocal EnableDelayedExpansion
for /f "tokens=*" %%i in ('dir /a:d /b') do (
    set /A a+=1
    ren "%%i" "!a!电影"
)
```

其中`电影`是每个文件夹所拥有的后缀

# 删去文件部分文件名

## 前缀

```bash
setlocal enabledelayedexpansion
for /f "delims=" %%i in ('dir /b [Sakurato.Sub] *') do (
    set var=%%i
    set var=!var:[Sakurato.Sub] =!
    ren "%%i" "!var!"
)
pause
```

其中`[Sakurato.Sub] `是每个文件所拥有的前缀

## 中缀

```bash
setlocal enabledelayedexpansion
for /f "delims=" %%i in ('dir /b *Nekopara*') do (
    set var=%%i
    set var=!var:Nekopara=!
    ren "%%i" "!var!"
)
pause
```

其中`Nekopara`是每个文件所拥有的中缀

## 后缀

```bash
setlocal enabledelayedexpansion
for /f "delims=" %%i in ('dir /b * (HEVC-10Bit-2160P AAC)*') do (
    set var=%%i
    set var=!var: (HEVC-10Bit-2160P AAC)=!
    ren "%%i" "!var!"
)
pause
```

其中` (HEVC-10Bit-2160P AAC)`是每个文件所拥有的后缀

# 替换部分文件名

```bash
setlocal enabledelayedexpansion
for /f "delims=" %%i in ('dir /b *Nekopara*') do (
    set var=%%i
    set var=!var:Nekopara=monika!
    ren "%%i" "!var!"
)
pause
```

其中`Nekopara`是每个文件所拥有的一部分，这一部分将会被`monika`所替换

# 自定义替换部分

请配合[如何优雅的批量重命名文件【高玩】](https://www.bilibili.com/video/BV1sy4y1z7gs/)使用

```bash
DIR *.* /B > list.csv
```

```bash
="ren "&A1&" "&B1&C1&".docx"
```
