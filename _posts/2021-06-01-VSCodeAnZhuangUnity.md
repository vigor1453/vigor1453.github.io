---
layout: post
title: "VSCode安装Unity"
date: 2021-06-01
tags: ["vscode"]
comments: true
author: oneman233
excerpt: "环境配置踩坑"
---

主要参考了官方给出的[Unity扩展安装指南](https://code.visualstudio.com/docs/other/unity)

但是在实际调试Unity项目的时候遇到了没有代码自动补全的问题，这时可以检查以下两项：

* `OmniSharp.path=latest`：C#的语法补全器，需要保持在最新版本
* 语言包不是DEV包：官方给出的.NET Core下载里是有多语言包的，别把它们跟DEV包搞混了

附上VSCode的[键盘快捷键](https://code.visualstudio.com/shortcuts/keyboard-shortcuts-windows.pdf)