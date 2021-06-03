---
layout: post
title: "VSCode安装C++"
date: 2021-06-03
tags: ["cpp","vscode"]
comments: true
author: oneman233
excerpt: "环境配置再踩坑"
---

主要参考了[这篇知乎的教程](https://zhuanlan.zhihu.com/p/77645306)，但是这篇教程讲得太细，从下载VSCode开始讲起，其实总结一下就下面几步：

1. 安装MinGW
2. 配置环境变量
3. VSCode安装C++相关扩展
4. 安装Coderunner扩展用来运行程序

默认的Coderunner有几个小问题，在这里列举一下：

1. 无法向默认的只读终端输入数据，也就是`scanf()`一类的函数用不了：设置一下`Run-in-external-terminal`即可，这样就可以在VSCode的终端中输入数据了

    BTW，`C/C++ Compile Run`扩展也是用来编译c/cpp的，直接使用`F6`即可
2. 添加`-lwsock32`编译命令：修改`code-runner.executorMap`为：
   `"cpp": "g++ $fullFileName -o $fileNameWithoutExt.exe -lwsock32 && $fileNameWithoutExt.exe"`
3. 想同时运行多个cpp文件，Coderunner却只能同时运行一个终端：添加一个`start`，即修改`code-runner.executorMap`为：
   `"cpp": "g++ $fullFileName -o $fileNameWithoutExt.exe && start $fileNameWithoutExt.exe"`
    这种方案并不完美，因为有时Coderunner又莫名其妙地可以多开，VSCode当中的终端是不需要`system("pause")`的，对于写算法代码来说实在是太友好了

此外，上面那篇教程中还有断点调试的部分，之后再进行学习