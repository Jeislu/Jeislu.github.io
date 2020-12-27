---
title: 美化Git Bash界面
date: 2020-12-27 18:36:45
tags:
 - 美化
 - 其他
---

## 前言

在写上一个博客的时候，不小心瞄到别人的Git，为什么他的那么好看，我也要！所以就有了这篇博客。首先本文的内容基本是搬运别人的，这边先贴上原作者[地址](https://www.rumosky.com/archives/388.html)

<!-- more -->

## 教程

打开`Git Bash`，切换路径

```bash
cd ~      # 即切换到C盘中的用户目录下
```

修改配置文件，可以通过下面的指令修改，也可以直接使用记事本打开文件修改，修改配置内容如下，然后重启即可。

```bash
vi .minttyrc    # 编辑配置文件
```

```properties
Font=Consolas
FontHeight=10

ForegroundColour=131,148,150
BackgroundColour=40,44,52
CursorColour=82,139,255

Black=40,44,52
BoldBlack=90,44,52
Red=224,108,117
BoldRed=255,108,117
Green=152,195,121
BoldGreen=152,245,121
Yellow=229,192,123
BoldYellow=229,192,173
Blue=97,175,239
BoldBlue=97,175,255
Magenta=198,120,221
BoldMagenta=248,120,221
Cyan=86,182,194
BoldCyan=86,232,194
White=171,178,191
BoldWhite=231,178,191
BoldAsFont=-1
FontSmoothing=full
FontWeight=700
Locale=C
Charset=UTF-8
Columns=80
Rows=28
```

可以根据自己的需求更改配置，我个人觉得它这个配色已经很好看了就没有去改。