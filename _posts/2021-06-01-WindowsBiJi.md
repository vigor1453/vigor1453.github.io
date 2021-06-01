---
layout: post
title: "Windows笔记"
date: 2021-06-01
tags: ["OS"]
comments: true
toc: true
author: oneman233
excerpt: "最常用的操作系统"
---

# 查看CPU信息

在命令行中输入`systeminfo`，`processor(s)`后就是物理CPU的信息

更信息的信息可以在命令行中输入以下命令：

```
wmic
cpu get *
```

其中`NumberOfCores`表示了当前CPU的核心数

# 在当前目录打开管理员命令行

建立以下reg文件：

```
Windows Registry Editor Version 5.00

; Created by: Shawn Brink

; http://www.sevenforums.com

; Tutorial: http://www.sevenforums.com/tutorials/47415-open-command-window-here-administrator.html

[-HKEY_CLASSES_ROOT\Directory\shell\runas]

[HKEY_CLASSES_ROOT\Directory\shell\runas]

@="Open cmd here as Admin"

"HasLUAShield"=""

[HKEY_CLASSES_ROOT\Directory\shell\runas\command]

@="cmd.exe /s /k pushd \"%V\""

[-HKEY_CLASSES_ROOT\Directory\Background\shell\runas]

[HKEY_CLASSES_ROOT\Directory\Background\shell\runas]

@="Open cmd here as Admin"

"HasLUAShield"=""

[HKEY_CLASSES_ROOT\Directory\Background\shell\runas\command]

@="cmd.exe /s /k pushd \"%V\""

[-HKEY_CLASSES_ROOT\Drive\shell\runas]

[HKEY_CLASSES_ROOT\Drive\shell\runas]

@="Open cmd here as Admin"

"HasLUAShield"=""

[HKEY_CLASSES_ROOT\Drive\shell\runas\command]

@="cmd.exe /s /k pushd \"%V\""
```

这是Windows的注册表文件，运行添加注册表后鼠标右键可以看到选项`Open cmd here as Admin`

[参考](https://www.cnblogs.com/Wayou/p/3359993.html)

# 生成目录文件树

可以使用原生的`tree`命令

[参考](http://sunshiyong.com/2018/05/13/tree-win/)