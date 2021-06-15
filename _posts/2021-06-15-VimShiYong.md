---
layout: post
title: "Vim使用"
date: 2021-06-15
tags: ["OS"]
toc: true
author: oneman233
excerpt: "神奇的编辑器"
---

最近在学习Linux下的网络编程，涉及到一些C程序的编写，于是用VMWare装了个Ubuntu虚拟机之后就打算直接在Ubuntu上写代码

实际上VSCode有更好的解决方案，可以参考[这篇文章](https://zhuanlan.zhihu.com/p/68577071)，这也正是我目前在使用的工具

* Vim的安装和配置参考了[这篇博客](https://www.jianshu.com/p/96dbc05d3df1)
* 修改配色方案的方式则参考了[这篇博客](https://blog.csdn.net/shuzfan/article/details/79006420)
* Vim的快捷键速查表可以参考[这里](https://linux.cn/article-8144-1.html)

但是使用Vim时候踩了个坑：`ctrl+S`在类Unix系统下用于**停止终端的输出**，无论你输入什么都好像终端卡死了一样，但是输入仍在继续

要想重新开始输出需要使用 `ctrl+Q`

解决方案参考了[这篇博客](https://blog.csdn.net/wangeen/article/details/8835501)

此外还有两个Linux下编程的小tips：

* Linux打开新的终端的快捷键是 `ctrl+alt+T`，关闭终端的快捷键是 `ctrl+alt+W`，可以用于C程序的调试当中
* 一步到位的gcc编译命令：`gcc -o -Wall my_program my_program.c`，这条指令还会输出尽可能多的警告信息